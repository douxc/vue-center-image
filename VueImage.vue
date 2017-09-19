<!-- 根据宽高比例显示图片
 Created by douxc on 2017/7/6.
-->
<template>
    <div :class="`vue-image-container ${inline?'vue-image-inline':''}`"
         :style="`max-width:${maxWidth};max-height:${maxWidth}`" @click="clickHandler">
        <div class="vue-image-view-container">
            <div v-if="isPDF" :class="`vue-image-pdf-view ${pdfStyle}`"></div>
            <img v-else="!isPDF" width="100%" height="100%" alt="img"/>
        </div>
    </div>
</template>
<script>
    /**
     * 获取图片原始大小
     */
    const _getImageOriginalSize = function (src) {
        return new Promise((resolve, reject) => {
            let _tmpImg = new Image();
            _tmpImg.src = src;
            if (_tmpImg.complete) {
                resolve({width: _tmpImg.width, height: _tmpImg.height});
                _tmpImg = null;
            } else {
                _tmpImg.onload = function () {
                    resolve({width: _tmpImg.width, height: _tmpImg.height});
                    _tmpImg = null;
                };
                _tmpImg.onerror = function () {
                    reject(`获取图片出错：${src}`);
                    _tmpImg = null;
                };
            }
        });
    };
    export default {
        name: 'vueImage',
        computed: {
            /**
             * 判断当前的地址是否是pdf文件
             */
            isPDF: function () {
                const {src} = this;
                return Object.is(src.substr(-3).toLocaleLowerCase(), 'pdf');
            }
        },
        methods: {
            /**
             * 计算图片大小，仅供内部调用
             * 如果请求地址不是合法的图片地址，则不会发送请求
             * @private
             */
            _calcImgSize() {
                const _this = this, {src, compress, isPDF} = this;
                // 先处理src为null或者不是img（pdf文件）的情况
                if (!src || isPDF) {
                    return false;
                }
                let _src = compress ? `${src}!compress` : src; // 判断是否请求压缩之后的地址
                const _imgEle = _this.$el.querySelector('img'); // 当前图片对象
                const _containerW = _this.$el.clientHeight || Number.parseInt(_this.maxWidth.replace('px', '')); // 容器宽度
                _imgEle.setAttribute('style', "visibility: hidden");
                // 获取图片原始大小后计算图片的显示
                _getImageOriginalSize(_src).then(({width, height}) => {
                    if (Object.is(width, height)) {
                        // 宽高相等
                        _imgEle.setAttribute('width', '100%');
                        _imgEle.setAttribute('height', '100%');
                    } else if (width > height) {
                        // 宽度更大，则以宽度为准，高度自适应
                        _imgEle.setAttribute('width', '100%');
                        _imgEle.setAttribute('height', 'auto');
                    } else {
                        // 高度更大，则高度为容器高度，宽度自适应
                        _imgEle.setAttribute('width', 'auto');
                        _imgEle.setAttribute('height', `${_containerW}px`);
                    }
                    _imgEle.setAttribute('style', "visibility: visible");
                    _imgEle.setAttribute('src', _src);
                }).catch(err => {
                    // 图片计算出错了，此时使用默认图片地址
                    _imgEle.setAttribute('style', "visibility: visible");
                    _imgEle.setAttribute('src', 'http://via.placeholder.com/128');
                    console.warn(err);
                });
                // 宽高计算完成了，显示图片，一定程度上可以避免闪屏
            },
            /**
             * 点击事件处理
             */
            clickHandler() {
                this.clickable && window.open(this.src);
            }
        },
        props: {
            // 是否显示为行内元素
            inline: {
                type: Boolean,
                default: false
            },
            // 最大宽度
            maxWidth: {
                type: String,
                default: '320px'
            },
            src: {
                required: true,
                type: String
            },
            // 是否可以点击，默认否
            clickable: {
                type: Boolean,
                default: false
            },
            // 是否开启请求压缩
            compress: {
                type: Boolean,
                default: true
            },
            pdfStyle: {
                type: String,
                default: 'vue-image-pdf'
            }
        },
        watch: {
            'src': function () {
                this._calcImgSize();
            }
        },
        mounted() {
            // 挂载之后，计算图片怎么显示
            this._calcImgSize();
        }
    };
</script>
<style>
    /*图片显示的区域*/

    .vue-image-container {
        width: 100%;
        height: 100%;
        position: relative;
        display: block;
    }

    /*显示区域设为正方形*/

    .vue-image-container::after {
        content: '';
        display: block;
        padding-top: 100%;
    }

    .vue-image-container .vue-image-view-container {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: #f9f9f9;
        border: 1px solid #f7f7f7;
        overflow: hidden;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    /*预览图*/

    .vue-image-container.vue-image-inline {
        display: inline-block;
        vertical-align: middle;
    }

    /*PDF文件预览图*/
    .vue-image-pdf-view {
        font-size: 480%;
    }

    .vue-image-pdf::before {
        content: 'PDF';
    }
</style>
