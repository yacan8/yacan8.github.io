---
title: antd源码解读(一)Form组件解析
date: 2018-04-15 15:53:11
tags:
- antd
- 前端
- react
---

### 引言
看过antd源码的都知道，antd其实是在一组[react-componment](https://github.com/react-component)组件的基础上进行了一层ui封装，本文主要解读antd组件Form的基础组件[react-componment/form](https://github.com/react-component/form)，另外会略过`development`模式下的warning代码。

### 入手 Form.create
解读源码首先要从自己最常用的或者感兴趣的入手，首先form组件最主要的还是在`Form.create({options})`这个装饰器入手。找到项目下的文件`createForm.js`，这个文件还是主要主要对`createBaseForm.js`文件进行了一层封装，提供了一些默认配置参数，下看查看`createBaseForm.js`里的createBaseForm方法，改方法主要是一个装饰器作用，包装一个高阶React组件，在props里注入一个值为`formPropName(默认为form)`变量，所有功能在这个变量里完成，主要内容如下
```js
render() {
  const { wrappedComponentRef, ...restProps } = this.props;
  const formProps = {
    [formPropName]: this.getForm(), // 来在 formPropName默认为form，getForm方法来自`createForm.js`
  };
  if (withRef) {
    formProps.ref = 'wrappedComponent';
  } else if (wrappedComponentRef) {
    formProps.ref = wrappedComponentRef;
  }
  const props = mapProps.call(this, {
    ...formProps,
    ...restProps,
  });
  return <WrappedComponent {...props} />;
}
```
在装饰器初始化的时候，Form初始化了一个只属于该组件实例的store，用来存放当前Form组件的一些输入的数据，主要代码如下:
```js
const fields = mapPropsToFields && mapPropsToFields(this.props);  // mapPropsToFields来自于Form.create的配置参数，用来转化来自mobx或者redux等真正的store来源的value，以初始化该Form实例的fieldsStore
this.fieldsStore = createFieldsStore(fields || {});  // createFieldsStore来自于文件`createFieldsStore.js`文件
```
### getFieldDecorator
柯里化函数，通过id与参数声明的输入，返回一个函数以输入组件为入参的函数，通过该函数声明的输入组件与表单Form双向数据绑定。
```js
  ...
  getFieldDecorator(name, fieldOption) {
    const props = this.getFieldProps(name, fieldOption);  // 初始化一个field
    return (fieldElem) => {
      const fieldMeta = this.fieldsStore.getFieldMeta(name);  // 获取变化（Form的onChange）后的field数据
      const originalProps = fieldElem.props;
      fieldMeta.originalProps = originalProps;  // 输入组件初始化时保存的Prop
      fieldMeta.ref = fieldElem.ref;
      return React.cloneElement(fieldElem, {
        ...props,
        ...this.fieldsStore.getFieldValuePropValue(fieldMeta),  // 获取prop属性 value
      });
    };
  }
  ...
```
往下看 `getFieldProps`
```js
  ...
  getFieldProps(name, usersFieldOption = {}) {
    if (!name) {
      throw new Error('Must call `getFieldProps` with valid name string!');
    }

    delete this.clearedFieldMetaCache[name];

    const fieldOption = {
      name,
      trigger: DEFAULT_TRIGGER,
      valuePropName: 'value',
      validate: [],
      ...usersFieldOption,
    };

    const {
      rules,
      trigger,
      validateTrigger = trigger,
      validate,
    } = fieldOption;

    const fieldMeta = this.fieldsStore.getFieldMeta(name);
    if ('initialValue' in fieldOption) {
      fieldMeta.initialValue = fieldOption.initialValue;
    }

    const inputProps = {
      ...this.fieldsStore.getFieldValuePropValue(fieldOption),
      ref: this.getCacheBind(name, `${name}__ref`, this.saveRef),
    };
    if (fieldNameProp) {
      inputProps[fieldNameProp] = name;
    }

    const validateRules = normalizeValidateRules(validate, rules, validateTrigger);
    const validateTriggers = getValidateTriggers(validateRules);
    validateTriggers.forEach((action) => {
      if (inputProps[action]) return;
      inputProps[action] = this.getCacheBind(name, action, this.onCollectValidate);
    });

    // make sure that the value will be collect
    if (trigger && validateTriggers.indexOf(trigger) === -1) {
      inputProps[trigger] = this.getCacheBind(name, trigger, this.onCollect);
    }

    const meta = {
      ...fieldMeta,
      ...fieldOption,
      validate: validateRules,
    };
    this.fieldsStore.setFieldMeta(name, meta);
    if (fieldMetaProp) {
      inputProps[fieldMetaProp] = meta;
    }

    if (fieldDataProp) {
      inputProps[fieldDataProp] = this.fieldsStore.getField(name);
    }

    return inputProps;
  },
  ...
```
