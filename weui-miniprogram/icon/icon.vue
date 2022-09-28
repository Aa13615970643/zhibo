<template>
    <view
        :class="extClass + ' weui-icon'"
        :style="
            'background:' +
            color +
            ';width:' +
            size +
            'px;height:' +
            (utils.ifSpecialIcon(icon) ? utils.double(size) : size) +
            'px;mask-image:url(' +
            src +
            ');-webkit-mask-image:url(' +
            src +
            ');-moz-mask-image:url(' +
            src +
            ')'
        "
    ></view>
</template>
<script module="utils" lang="wxs">
var double = function(a) {
    return 2*a
};
var ifSpecialIcon = function(v) {
  return v === 'arrow' || v === 'back'
}
module.exports = {
  double: double,
  ifSpecialIcon: ifSpecialIcon
}
</script>

<style>
.weui-icon {
    vertical-align: middle;
    display: inline-block;
    background: black;
    mask-repeat: no-repeat;
    -webkit-mask-repeat: no-repeat;
    -moz-mask-repeat: no-repeat;
    mask-size: cover;
    -webkit-mask-size: cover;
    -moz-mask-size: cover;
}
</style>
