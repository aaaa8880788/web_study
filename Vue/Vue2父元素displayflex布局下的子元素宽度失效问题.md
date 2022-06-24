# 父元素display:flex布局下的子元素宽度无效问题

因为设置了display: flex; 导致block布局变成了flex布局， 所以在子元素宽度没有被撑破的情况下，子元素宽度是有效的，但是当子元素内容过多，此时宽度会比实际宽度小，所以如果想要在已经设置了flex布局的基础上，再进行子元素宽度的设置，可以应用下面的样式：（在该子元素上设置）

设置了（display: flex；）后，子元素如果设置了宽度，在父元素宽度没被撑破时有效，但是一旦撑破，子元素宽度就会失效（会变小），解决方法可以在子元素加个（flex-shrink: 0;）这样就不会失效

```
<div class="father">
    <div class="children">
        <span>哈哈哈</span>
    </div>
    <div class="children">
        <span>哈哈哈</span>
    </div><div class="children">
        <span>哈哈哈</span>
    </div>
</div>

.father{
    display:flex;
    width:100px;
    .children{
        width:50px;
        flex-shrink: 0;
    }
}
```

\

