# element UI源码

element ui关于主题颜色定义在package/theme-chalk中，可以找到对应的组件的样式,element UI使用BEM命名规范

+ **BEM** 命名规范（块（block）,元素（element）,修饰符（modifier））

  ```
  如：type-block__element--modifier
     el-notification__group
  type: 前缀,element UI使用的是el
  block:块，比如一块都表示消息通知，notification
  element：块的直接子元素
  modifier：修饰符，特定的属性，比如表示size的small/normal/big
  ```

源码如何实现这种命名

```scss
// $namespace值为el，传入notification，会得到el-notification
@mixin b($block) {
  $B: $namespace+'-'+$block !global;
 
  .#{$B} {
    @content;
  }
}

@mixin e($element) {
  $E: $element !global;
  $selector: &;
  $currentSelector: "";
  @each $unit in $element {
    $currentSelector: #{$currentSelector + "." + $B + $element-separator + $unit + ","};
  }
 
  @if hitAllSpecialNestRule($selector) {
    @at-root {
      #{$selector} {
        #{$currentSelector} {
          @content;
        }
      }
    }
  } @else {
    @at-root {
      #{$currentSelector} {
        @content;
      }
    }
  }
}
 
@mixin m($modifier) {
  $selector: &;
  $currentSelector: "";
  @each $unit in $modifier {
    $currentSelector: #{$currentSelector + & + $modifier-separator + $unit + ","};
  }
 
  @at-root {
    #{$currentSelector} {
      @content;
    }
  }
}
```

