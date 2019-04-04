<template>
    <!-- Root element of PhotoSwipe. Must have class pswp. -->
    <div class="pswp svipropswp" tabindex="-1" role="dialog" aria-hidden="true">

        <!-- Background of PhotoSwipe. 
            It's a separate element as animating opacity is faster than rgba(). -->
        <div class="pswp__bg"></div>

        <!-- Slides wrapper with overflow:hidden. -->
        <div class="pswp__scroll-wrap">

            <!-- Container that holds slides. 
                PhotoSwipe keeps only 3 of them in the DOM to save memory.
                Don't modify these 3 pswp__item elements, data is added later on. -->
            <div class="pswp__container">
                <div class="pswp__item"></div>
                <div class="pswp__item"></div>
                <div class="pswp__item"></div>
            </div>

            <!-- Default (PhotoSwipeUI_Default) interface on top of sliding area. Can be changed. -->
            <div class="pswp__ui pswp__ui--hidden">

                <div class="pswp__top-bar">

                    <!--  Controls are self-explanatory. Order can be changed. -->

                    <div class="pswp__counter"></div>

                    <button class="pswp__button pswp__button--close" title="Close (Esc)"></button>

                    <button class="pswp__button pswp__button--share" title="Share"></button>

                    <button class="pswp__button pswp__button--fs" title="Toggle fullscreen"></button>

                    <button class="pswp__button pswp__button--zoom" title="Zoom in/out"></button>

                    <!-- Preloader demo http://codepen.io/dimsemenov/pen/yyBWoR -->
                    <!-- element will get class pswp__preloader--active when preloader is running -->
                    <div class="pswp__preloader">
                        <div class="pswp__preloader__icn">
                            <div class="pswp__preloader__cut">
                                <div class="pswp__preloader__donut"></div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="pswp__share-modal pswp__share-modal--hidden pswp__single-tap">
                    <div class="pswp__share-tooltip"></div>
                </div>

                <button class="pswp__button pswp__button--arrow--left" title="Previous (arrow left)">
                    <span class="dashicons dashicons-arrow-left-alt2"></span>
                </button>

                <button class="pswp__button pswp__button--arrow--right" title="Next (arrow right)">
                    <span class="dashicons dashicons-arrow-right-alt2"></span>
                </button>

                <div class="pswp__caption" :class="[thumbnails?'svicaption-top':'']">
                    <div class="pswp__caption__center"></div>
                </div>

            </div>

        </div>
        <div class="flexslider-svi">
            <div v-if="thumbnails" class="arrow-down togglehidesvi" v-on:click='toggle()'>
                <span class="dashicons" :class='"dashicons-arrow-"+up+"-alt2"'></span>
            </div>
            <div v-if="thumbnails" class="swiper-container gallery-pswp">
                <div class="swiper-wrapper">
                    <div class="swiper-slide" v-for="(item) in items.slider" :key="item.key">
                        <img :src="item.single.thumb_image" :alt="item.single.alt" :srcset="item.single.srcset" :sizes="item.single.sizes"
                            :width="item.single.width" :height="item.single.height">
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import "swiper/dist/css/swiper.css";

    import PhotoSwipe from 'photoswipe/dist/photoswipe'
    import PhotoSwipeDefaultUI from 'photoswipe/dist/photoswipe-ui-default'

    export default {
        data: function () {
            return {
                thumbnails: false,
                items: [],
                up: 'down',
                mswiper: false,
            }
        },
        mounted: function () {},
        methods: {
            open(index, items, thumbnails, options) {
                let _this = this;
                this.thumbnails = thumbnails;
                this.items = items;
                this.photoswipe = new PhotoSwipe(this.$el, PhotoSwipeDefaultUI, items.box, options);
                this.photoswipe.init();
                var handle_visible = setInterval(function () {

                    if ($jQsvi('.pswp').is(":visible")) {
                        setTimeout(function () {
                            $jQsvi('.togglehidesvi').hide();

                            let swiperOptionTop = {
                                slidesPerView: 10,
                                spaceBetween: 10,
                                autoHeight: true,
                                width: 100,
                                centeredSlides: true,
                                slideToClickedSlide: true,
                                watchSlidesVisibility: true,
                                observer: true,
                                on: {
                                    init: () => {
                                        $jQsvi('.togglehidesvi').slideDown();
                                        _this.initClick();
                                    },
                                    slideChange: () => {
                                        _this.onSlideChange();
                                    }
                                },
                                breakpoints: {
                                    // when window width is <= 320px
                                    320: {
                                        slidesPerView: 2,
                                        spaceBetween: 10
                                    },
                                    // when window width is <= 480px
                                    480: {
                                        slidesPerView: 3,
                                        spaceBetween: 20
                                    },
                                    // when window width is <= 640px
                                    640: {
                                        slidesPerView: 4,
                                        spaceBetween: 30
                                    },
                                }
                            };

                            _this.mswiper = new _this.$swiper($jQsvi(".gallery-pswp"), swiperOptionTop);

                        }, 200)
                        clearInterval(handle_visible);
                    }
                }, 5);


            },
            onSlideChange() {
                this.photoswipe.goTo(this.mswiper.activeIndex);
            },
            initClick() {
                setTimeout(() => {
                    this.mswiper.update();
                }, 300)
                var _this = this;
                var slider = $jQsvi('.gallery-pswp');
                var slide = $jQsvi("div.gallery-pswp .swiper-slide");

                $jQsvi("div.gallery-pswp .swiper-slide").eq(this.photoswipe.getCurrentIndex()).addClass('svifaded');



                this.photoswipe.listen('afterChange', function () {
                    $jQsvi('div.gallery-pswp .swiper-slide').removeClass("svifaded");
                    _this.mswiper.slideTo(_this.photoswipe.getCurrentIndex())
                    $jQsvi("div.gallery-pswp .swiper-slide").eq(_this.photoswipe.getCurrentIndex()).addClass(
                        'svifaded');
                });

                this.photoswipe.listen('close', function () {
                    $jQsvi('div.gallery-pswp .swiper-slide').removeClass("svifaded");
                });

                slider.slideDown();
                $jQsvi('.togglehidesvi').slideDown();
            },
            toggle() {
                var slider = $jQsvi('div.gallery-pswp');
                if (this.up = 'down')
                    this.up = 'up'
                else
                    this.up = 'down';
                slider.toggle();
            },
            close() {
                this.mswiper.destroy();
                this.photoswipe.close()
            }
        }
    }
</script>
