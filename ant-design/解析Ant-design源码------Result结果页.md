## Result结果

用于反馈一系列操作任务的处理结果。

### 何时使用

当有重要操作需告知用户处理结果，且反馈内容较为复杂时使用。

### 源码

```jsx
import * as React from 'react';
import classNames from 'classnames';
import CheckCircleFilled from '@ant-design/icons/CheckCircleFilled';
import CloseCircleFilled from '@ant-design/icons/CloseCircleFilled';
import ExclamationCircleFilled from '@ant-design/icons/ExclamationCircleFilled';
import WarningFilled from '@ant-design/icons/WarningFilled';

import { ConfigContext } from '../config-provider';
import devWarning from '../_util/devWarning';

import noFound from './noFound';
import serverError from './serverError';
import unauthorized from './unauthorized';

export const IconMap = {
  success: CheckCircleFilled,
  error: CloseCircleFilled,
  info: ExclamationCircleFilled,
  warning: WarningFilled,
};

export const ExceptionMap = {
  '404': noFound,
  '500': serverError,
  '403': unauthorized,
};

export type ExceptionStatusType = 403 | 404 | 500 | '403' | '404' | '500';
export type ResultStatusType = ExceptionStatusType | keyof typeof IconMap;

export interface ResultProps {
  icon?: React.ReactNode;
  status?: ResultStatusType;
  title?: React.ReactNode;
  subTitle?: React.ReactNode;
  extra?: React.ReactNode;
  prefixCls?: string;
  className?: string;
  style?: React.CSSProperties;
}

// ExceptionImageMap keys
const ExceptionStatus = Object.keys(ExceptionMap);

/**
 * Render icon if ExceptionStatus includes ,render svg image else render iconNode
 *
 * @param prefixCls
 * @param {status, icon}
 */
const renderIcon = (prefixCls: string, { status, icon }: ResultProps) => {
  const className = classNames(`${prefixCls}-icon`);

  devWarning(
    !(typeof icon === 'string' && icon.length > 2),
    'Result',
    `\`icon\` is using ReactNode instead of string naming in v4. Please check \`${icon}\` at https://ant.design/components/icon`,
  );

  if (ExceptionStatus.includes(`${status}`)) {
    const SVGComponent = ExceptionMap[status as ExceptionStatusType];
    return (
      <div className={`${className} ${prefixCls}-image`}>
        <SVGComponent />
      </div>
    );
  }
  const iconNode = React.createElement(
    IconMap[status as Exclude<ResultStatusType, ExceptionStatusType>],
  );

  return <div className={className}>{icon || iconNode}</div>;
};

const renderExtra = (prefixCls: string, { extra }: ResultProps) =>
  extra && <div className={`${prefixCls}-extra`}>{extra}</div>;

export interface ResultType extends React.FC<ResultProps> {
  PRESENTED_IMAGE_404: React.ReactNode;
  PRESENTED_IMAGE_403: React.ReactNode;
  PRESENTED_IMAGE_500: React.ReactNode;
}

const Result: ResultType = ({
  prefixCls: customizePrefixCls,
  className: customizeClassName,
  subTitle,
  title,
  style,
  children,
  status = 'info',
  icon,
  extra,
}) => {
  const { getPrefixCls, direction } = React.useContext(ConfigContext);

  const prefixCls = getPrefixCls('result', customizePrefixCls);
  const className = classNames(prefixCls, `${prefixCls}-${status}`, customizeClassName, {
    [`${prefixCls}-rtl`]: direction === 'rtl',
  });
  return (
    <div className={className} style={style}>
      {renderIcon(prefixCls, { status, icon })}
      <div className={`${prefixCls}-title`}>{title}</div>
      {subTitle && <div className={`${prefixCls}-subtitle`}>{subTitle}</div>}
      {renderExtra(prefixCls, { extra })}
      {children && <div className={`${prefixCls}-content`}>{children}</div>}
    </div>
  );
};

Result.PRESENTED_IMAGE_403 = ExceptionMap['403'];
Result.PRESENTED_IMAGE_404 = ExceptionMap['404'];
Result.PRESENTED_IMAGE_500 = ExceptionMap['500'];

export default Result
```

该页面只负责布局和展示，内置了七种状态`success` \| `error` \| `info` \| `warning` \| `404` \| `403` \| `500`。

布局分为：icon图标区、title一级标题区、subTitle二级标题区、extra操作区、content内容区。

#### icon图标区

```jsx
const renderIcon = (prefixCls: string, { status, icon }: ResultProps) => {
  const className = classNames(`${prefixCls}-icon`)

  if (ExceptionStatus.includes(`${status}`)) {
    const SVGComponent = ExceptionMap[status as ExceptionStatusType];
    return (
      <div className={`${className} ${prefixCls}-image`}>
        <SVGComponent />
      </div>
    );
  }
  const iconNode = React.createElement(
    IconMap[status as Exclude<ResultStatusType, ExceptionStatusType>],
  );

  return <div className={className}>{icon || iconNode}</div>;
};
```

```css
  &-success &-icon > .@{iconfont-css-prefix} {
    color: @success-color;
  }

  &-error &-icon > .@{iconfont-css-prefix} {
    color: @error-color;
  }

  &-info &-icon > .@{iconfont-css-prefix} {
    color: @info-color;
  }

  &-warning &-icon > .@{iconfont-css-prefix} {
    color: @warning-color;
  }

  // Exception Status image
  &-image {
    width: 250px;
    height: 295px;
    margin: auto;
  }

  &-icon {
    margin-bottom: 24px;
    text-align: center;

    > .@{iconfont-css-prefix} {
      font-size: @result-icon-font-size;
    }
  }
```

如果用户传入了status，判断是否是内置的status，是则渲染对应的icon，如果用户同时传入了icon那么则展示icon。

#### title一级标题区

```css
  &-title {
    color: @heading-color;
    font-size: @result-title-font-size;
    line-height: 1.8;
    text-align: center;
  }
```

#### subTitle二级标题区

```css
  &-subtitle {
    color: @text-color-secondary;
    font-size: @result-subtitle-font-size;
    line-height: 1.6;
    text-align: center;
  }
```

#### extra操作区

```jsx
const renderExtra = (prefixCls: string, { extra }: ResultProps) =>
  extra && <div className={`${prefixCls}-extra`}>{extra}</div>;
```

```css
  &-extra {
    margin: @result-extra-margin;
    text-align: center;

    > * {
      margin-right: 8px;

      &:last-child {
        margin-right: 0;
      }
    }
  }
```

渲染操作区，传入为jsx数组，最后一个元素的margin-right为0，其余元素的margin-right为8px；

#### content内容区

```jsx
{children && <div className={`${prefixCls}-content`}>{children}</div>}
```

```css
  &-content {
    margin-top: 24px;
    padding: 24px 40px;
    background-color: @background-color-light;
  }
```

通过children渲染

### API

| 参数       | 说明            | 类型                                                                     | 默认值    |
| -------- | ------------- | ---------------------------------------------------------------------- | ------ |
| extra    | 操作区           | ReactNode                                                              | -      |
| icon     | 自定义 icon      | ReactNode                                                              | -      |
| status   | 结果的状态，决定图标和颜色 | `success` \| `error` \| `info` \| `warning` \| `404` \| `403` \| `500` | `info` |
| subTitle | subTitle 文字   | ReactNode                                                              | -      |
| title    | title 文字      | ReactNode                                                              | -      |
