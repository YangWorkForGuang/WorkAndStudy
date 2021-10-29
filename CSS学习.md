# Element-UI组件

## el-table

### el-table添加复选框

添加复选框很容易实现

```html
<el-table-column type="selection"></el-table-column>
```

但是复选框一列的表头无法显示任何内容

此时需要对CSS样式进行修改

```css
.el-table /deep/.DisabledSelection .cell .el-checkbox__inner{
  display:none;
  position:relative;
}
.el-table /deep/.DisabledSelection .cell:before{
  content:"选择";
  position:absolute;
  right 11px;
}
```

