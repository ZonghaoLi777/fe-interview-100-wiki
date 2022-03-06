# px em rem vw/vh 的区别

## 题目

px % em rem vw/vh 的区别

## px

像素，基本单位

## %

相对于父元素的尺寸。

如根据 `position: absolute;` 居中显示时，需要设置 `left: 50%`

```css
.container {
    with: 200px;
    height: 200px;
    position: relative;
}
.box {
    with: 100px;
    height: 100px;
    position: absolute;
    left: 50%;
    top: 50%;
    margin-top: -50px;
    margin-left: -50px;
}
```

## em

相对于当前元素的 `font-size`。首行缩进可以使用 `text-indent: 2em`。

## rem

rem = root em

相对于根元素的 `font-size` 。可以根据媒体查询，设置根元素的 `font-size` ，实现移动端适配。

```css
@media only screen and (max-width: 374px) {
    /* iphone5 或者更小的尺寸，以 iphone5 的宽度（320px）比例设置 font-size */
    html {
        font-size: 86px;
    }
}
@media only screen and (min-width: 375px) and (max-width: 413px) {
    /* iphone6/7/8 和 iphone x */
    html {
        font-size: 100px;
    }
}
@media only screen and (min-width: 414px) {
    /* iphone6p 或者更大的尺寸，以 iphone6p 的宽度（414px）比例设置 font-size */
    html {
        font-size: 110px;
    }
}
```

## vw/vh

- vw 屏幕宽度的 1%
- vh 屏幕高度的 1%
- vmin 两者最小值
- vmax 两者最大值
