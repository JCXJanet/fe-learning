## Divider

### 何时使用

- 对不同章节的文本段落进行分割。
- 对行内文字/链接进行分割，例如表格的操作列。

### 基础源码

```jsx
import * as React from 'react';
import classNames from 'classnames';
import { ConfigConsumer, ConfigConsumerProps } from '../config-provider';

export interface DividerProps {
  prefixCls?: string;
  type?: 'horizontal' | 'vertical';
  orientation?: 'left' | 'right' | 'center';
  className?: string;
  children?: React.ReactNode;
  dashed?: boolean;
  style?: React.CSSProperties;
  plain?: boolean;
}

const Divider: React.FC<DividerProps> = props => (
  <ConfigConsumer>
    {({ getPrefixCls, direction }: ConfigConsumerProps) => {
      const {
        prefixCls: customizePrefixCls,
        type = 'horizontal',
        orientation = 'center',
        className,
        children,
        dashed,
        plain,
        ...restProps
      } = props;
      const prefixCls = getPrefixCls('divider', customizePrefixCls);
      const orientationPrefix = orientation.length > 0 ? `-${orientation}` : orientation;
      const hasChildren = !!children;
      const classString = classNames(
        prefixCls,
        `${prefixCls}-${type}`,
        {
          [`${prefixCls}-with-text`]: hasChildren,
          [`${prefixCls}-with-text${orientationPrefix}`]: hasChildren,
          [`${prefixCls}-dashed`]: !!dashed,
          [`${prefixCls}-plain`]: !!plain,
          [`${prefixCls}-rtl`]: direction === 'rtl',
        },
        className,
      );
      return (
        <div className={classString} {...restProps} role="separator">
          {children && <span className={`${prefixCls}-inner-text`}>{children}</span>}
        </div>
      );
    }}
  </ConfigConsumer>
);

export default Divider;
```

其中interface对应提供的属性，组件先从Context中取出getPrefixCls方法和direction值。并设定了，默认type为horizntal，orientation为center。

通过getPrefixCls方法获得className的前缀。

orientation值只有3种，left、right、center，因此最后获得的值只有“-left”、“-right”、“-center”

hasChildren，得到是否有children传入。

classString，根据不同的属性值，获得不同的className。

dom结构，根据是否有children传入，渲染不同的dom结构。



### 水平分割线

默认为水平分割线，可在中间加入文字。

```jsx
import { Divider } from 'antd';

ReactDOM.render(
  <>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
    <Divider />
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
    <Divider dashed />
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
  </>,
  mountNode,
);
```

#### 源码

通过border-top实现分割线

```css
border-top: @border-width-base solid @divider-color;

&-horizontal {
   display: flex;
   clear: both;
   width: 100%;
   min-width: 100%; // Fix https://github.com/ant-design/ant-design/issues/10914
   margin: 24px 0;
}  
```

通过border-style实现虚线

```css
 &-dashed {
   background: none;
   border-color: @divider-color;
   border-style: dashed;
   border-width: @border-width-base 0 0;
 }

 &-vertical&-dashed {
   border-width: 0 0 0 @border-width-base;
 }
```

### 垂直分割线

使用 `type="vertical"` 设置为行内的垂直分割线。

```jsx
import { Divider } from 'antd';

ReactDOM.render(
  <>
    Text
    <Divider type="vertical" />
    <a href="#">Link</a>
    <Divider type="vertical" />
    <a href="#">Link</a>
  </>,
  mountNode,
);
```

#### 源码

```css
&-vertical {
   position: relative;
   top: -0.06em;
   display: inline-block;
   height: 0.9em;
   margin: 0 8px;
   vertical-align: middle;
   border-top: 0;
   border-left: @border-width-base solid @divider-color;
 }
```

通过border-left实现垂直分割线

### 带文字的分割线

分割线中带有文字，可以用 `orientation` 指定文字位置。

```jsx
import { Divider } from 'antd';

ReactDOM.render(
  <>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
    <Divider>Text</Divider>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
    <Divider orientation="left">Left Text</Divider>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
    <Divider orientation="right">Right Text</Divider>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
  </>,
  mountNode,
);
```

#### 源码

```css
  &-horizontal&-with-text {
    display: flex;
    margin: 16px 0;
    color: @heading-color;
    font-weight: 500;
    font-size: @font-size-lg;
    white-space: nowrap;
    text-align: center;
    border-top: 0;
    border-top-color: @divider-color;

    &::before,
    &::after {
      position: relative;
      top: 50%;
      width: 50%;
      border-top: @border-width-base solid transparent;
      // Chrome not accept `inherit` in `border-top`
      border-top-color: inherit;
      border-bottom: 0;
      transform: translateY(50%);
      content: '';
    }
  }

	&-horizontal&-with-text-left {
    &::before {
      top: 50%;
      width: @divider-orientation-margin;
    }

    &::after {
      top: 50%;
      width: 100% - @divider-orientation-margin;
    }
  }

  &-horizontal&-with-text-right {
    &::before {
      top: 50%;
      width: 100% - @divider-orientation-margin;
    }

    &::after {
      top: 50%;
      width: @divider-orientation-margin;
    }
  }

  &-inner-text {
    display: inline-block;
    padding: 0 @divider-text-padding;
  }
  
  &-horizontal&-with-text&-dashed {
    border-top: 0;

    &::before,
    &::after {
      border-style: dashed none none;
    }
  }
```

在分割线中使用文字，需要传入children，通过伪元素实现，且只支持水平样式。

当orientation选择为left时，左侧固定，右侧自适应。当orientation选择为right时，右侧固定，左侧自适应。当orientation选择为center时，两侧各为50%宽度。

此时分割线由伪元素的border-top实现，而原先在div上的border-top被置为0。

### 分割文字使用正文样式

使用 `plain` 可以设置为更轻量的分割文字样式。

```jsx
import { Divider } from 'antd';

ReactDOM.render(
  <>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
    <Divider plain>Text</Divider>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
    <Divider orientation="left" plain>
      Left Text
    </Divider>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
    <Divider orientation="right" plain>
      Right Text
    </Divider>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed nonne merninisti licere mihi ista
      probare, quae sunt a te dicta? Refert tamen, quo modo.
    </p>
  </>,
  mountNode,
);
```

#### 源码

```css
  &-plain&-with-text {
    color: @text-color;
    font-weight: normal;
    font-size: @font-size-base;
  }
```

正文样式

### 额外

同理的rtl实现

```css
@import '../../style/themes/index';
@import '../../style/mixins/index';

@divider-prefix-cls: ~'@{ant-prefix}-divider';

.@{divider-prefix-cls} {
  &-rtl {
    direction: rtl;
  }

  &-horizontal&-with-text-left {
    &::before {
      .@{divider-prefix-cls}-rtl& {
        width: 100% - @divider-orientation-margin;
      }
    }

    &::after {
      .@{divider-prefix-cls}-rtl& {
        width: @divider-orientation-margin;
      }
    }
  }

  &-horizontal&-with-text-right {
    &::before {
      .@{divider-prefix-cls}-rtl& {
        width: @divider-orientation-margin;
      }
    }

    &::after {
      .@{divider-prefix-cls}-rtl& {
        width: 100% - @divider-orientation-margin;
      }
    }
  }
}
```



### API

| 参数        | 说明                       | 类型                          | 默认值       | 版本  |
| ----------- | -------------------------- | ----------------------------- | ------------ | ----- |
| className   | 分割线样式类               | string                        | -            |       |
| dashed      | 是否虚线                   | boolean                       | false        |       |
| orientation | 分割线标题的位置           | `left` \| `right` \| `center` | `center`     |       |
| plain       | 文字是否显示为普通正文样式 | boolean                       | false        | 4.2.0 |
| style       | 分割线样式对象             | CSSProperties                 | -            |       |
| type        | 水平还是垂直类型           | `horizontal` \| `vertical`    | `horizontal` |       |

