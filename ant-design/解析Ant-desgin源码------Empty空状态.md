## Empty空状态

空状态时的展示占位图。

### 何时使用

- 当目前没有数据时，用于显式的用户提示。
- 初始化场景时的引导创建流程。

### 源码

```jsx
import * as React from 'react';
import classNames from 'classnames';
import { ConfigContext } from '../config-provider';
import LocaleReceiver from '../locale-provider/LocaleReceiver';
import DefaultEmptyImg from './empty';
import SimpleEmptyImg from './simple';

const defaultEmptyImg = <DefaultEmptyImg />;
const simpleEmptyImg = <SimpleEmptyImg />;

export interface TransferLocale {
  description: string;
}

export interface EmptyProps {
  prefixCls?: string;
  className?: string;
  style?: React.CSSProperties;
  /** @since 3.16.0 */
  imageStyle?: React.CSSProperties;
  image?: React.ReactNode;
  description?: React.ReactNode;
  children?: React.ReactNode;
}

interface EmptyType extends React.FC<EmptyProps> {
  PRESENTED_IMAGE_DEFAULT: React.ReactNode;
  PRESENTED_IMAGE_SIMPLE: React.ReactNode;
}

const Empty: EmptyType = ({
  className,
  prefixCls: customizePrefixCls,
  image = defaultEmptyImg,
  description,
  children,
  imageStyle,
  ...restProps
}) => {
  const { getPrefixCls, direction } = React.useContext(ConfigContext);

  return (
    <LocaleReceiver componentName="Empty">
      {(locale: TransferLocale) => {
        const prefixCls = getPrefixCls('empty', customizePrefixCls);
        const des = typeof description !== 'undefined' ? description : locale.description;
        const alt = typeof des === 'string' ? des : 'empty';

        let imageNode: React.ReactNode = null;

        if (typeof image === 'string') {
          imageNode = <img alt={alt} src={image} />;
        } else {
          imageNode = image;
        }

        return (
          <div
            className={classNames(
              prefixCls,
              {
                [`${prefixCls}-normal`]: image === simpleEmptyImg,
                [`${prefixCls}-rtl`]: direction === 'rtl',
              },
              className,
            )}
            {...restProps}
          >
            <div className={`${prefixCls}-image`} style={imageStyle}>
              {imageNode}
            </div>
            {des && <div className={`${prefixCls}-description`}>{des}</div>}
            {children && <div className={`${prefixCls}-footer`}>{children}</div>}
          </div>
        );
      }}
    </LocaleReceiver>
  );
};

Empty.PRESENTED_IMAGE_DEFAULT = defaultEmptyImg;
Empty.PRESENTED_IMAGE_SIMPLE = simpleEmptyImg;

export default Empty;
```

Empty空状态主要分为image图片区、des描述区、content内容区

#### image图片区

```jsx
let imageNode: React.ReactNode = null;

if (typeof image === 'string') {
  imageNode = <img alt={alt} src={image} />;
} else {
  imageNode = image;
}

<div className={`${prefixCls}-image`} style={imageStyle}>
    {imageNode}
</div>
```

```css
  &-image {
    height: 100px;
    margin-bottom: 8px;

    img {
      height: 100%;
    }

    svg {
      height: 100%;
      margin: auto;
    }
  }
```

如果image属性为string的话，则转为img元素。如果传入为ReactNode，则渲染ReactNode。同时，可以通过imageStyle属性去改变样式。

#### des描述区

```jsx
{des && <div className={`${prefixCls}-description`}>{des}</div>}
```

描述区

#### content内容区

```jsx
{children && <div className={`${prefixCls}-footer`}>{children}</div>}
```

```css
  &-footer {
    margin-top: 16px;
  }
```

通过children去渲染内容区

### API

| 参数        | 说明                                           | 类型          | 默认值                          | 版本 |
| ----------- | ---------------------------------------------- | ------------- | ------------------------------- | ---- |
| description | 自定义描述内容                                 | ReactNode     | -                               |      |
| image       | 设置显示图片，为 string 时表示自定义图片地址。 | ReactNode     | `Empty.PRESENTED_IMAGE_DEFAULT` |      |
| imageStyle  | 图片样式                                       | CSSProperties | -                               |      |

## 内置图片

```html
- Empty.PRESENTED_IMAGE_SIMPLE

  <div class="site-empty-buildIn-img site-empty-buildIn-simple"><div>

- Empty.PRESENTED_IMAGE_DEFAULT

  <div class="site-empty-buildIn-img site-empty-buildIn-default"></div>

<style>
  .site-empty-buildIn-img {
    background-repeat: no-repeat;
    background-size: contain;
  }
  .site-empty-buildIn-simple {
    width: 55px;
    height: 35px;
    background-image: url("https://user-images.githubusercontent.com/507615/54591679-b0ceb580-4a65-11e9-925c-ad15b4eae93d.png");
  }
  .site-empty-buildIn-default {
    width: 121px;
    height: 116px;
    background-image: url("https://user-images.githubusercontent.com/507615/54591670-ac0a0180-4a65-11e9-846c-e55ffce0fe7b.png");
  }
  [data-theme="dark"] .site-empty-buildIn-simple {
    background-image: url("https://gw.alipayobjects.com/zos/antfincdn/ldFsHUh3Xh/ea62c5fe-07bb-4fcd-9d35-19220cef372e.png");
  }
  [data-theme="dark"] .site-empty-buildIn-default {
    background-image: url("https://gw.alipayobjects.com/mdn/rms_08e378/afts/img/A*gfq-SoT3wF0AAAAAAAAAAABkARQnAQ");
  }
</style>
```

