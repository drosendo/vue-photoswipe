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
                </button>

                <button class="pswp__button pswp__button--arrow--right" title="Next (arrow right)">
                </button>

                <div class="pswp__caption" :class="[thumbnails?'svicaption-top':'']">
                    <div class="pswp__caption__center"></div>
                </div>

            </div>

        </div>
        <div v-if="thumbnails" class="arrow-down togglehidesvi" v-on:click='toggle()' onclick="this.classList.toggle('active')"></div>
        <div v-if="thumbnails" class="photoSwipe_innerthumbs flexslider">
            <ul class="slides">
                <li v-for="(item,k) in items" :key='k' class='svili'><img :src="item.msrc" /></li>
            </ul>
        </div>
    </div>
</template>

<script>
    import 'photoswipe/dist/photoswipe.css'
    import 'photoswipe/dist/default-skin/default-skin.css'

    import PhotoSwipe from 'photoswipe/dist/photoswipe'
    import PhotoSwipeDefaultUI from 'photoswipe/dist/photoswipe-ui-default'
    import flexslider from 'flexslider';

    export default {
        data: function () {
            return {
                thumbnails: false,
                items: []
            }
        },
        mounted: function () {},
        methods: {
            open(index, items, thumbnails, options) {
                let _this = this;
                this.thumbnails = thumbnails;
                this.items = items;
                this.photoswipe = new PhotoSwipe(this.$el, PhotoSwipeDefaultUI, items, options);
                this.photoswipe.init();

                var handle_visible = setInterval(function () {
                    if ($('.pswp').is(":visible")) {
                        $('.photoSwipe_innerthumbs,.togglehidesvi').hide();

                        $.flexslider($('.photoSwipe_innerthumbs.flexslider'), {
                            animation: "slide",
                            controlNav: false,
                            animationLoop: false,
                            barsSize: {
                                top: 44,
                                bottom: 44
                            },
                            slideshow: true,
                            itemWidth: 100,
                            itemMargin: 5,
                            prevText: "", //String: Set the text for the "previous" directionNav item
                            nextText: "",
                            start: function (slider) {
                                if (slider.pagingCount === 1)
                                    slider.addClass('flex-centered');

                                _this.initClick();
                            }
                        });

                        clearInterval(handle_visible);
                    }
                }, 5);


            },
            initClick() {
                var _this = this;
                var slider = $('.photoSwipe_innerthumbs');
                $("div.photoSwipe_innerthumbs img").eq(_this.photoswipe.getCurrentIndex()).addClass('svifaded');

                jQuery(_this.$el).on('click', 'div.photoSwipe_innerthumbs img', function (e) {
                    $('div.photoSwipe_innerthumbs img').removeClass("svifaded");
                    _this.photoswipe.goTo($("div.photoSwipe_innerthumbs img").index($(this)));
                });

                this.photoswipe.listen('afterChange', function () {
                    $('div.photoSwipe_innerthumbs img').removeClass("svifaded");
                    $("div.photoSwipe_innerthumbs img").eq(_this.photoswipe.getCurrentIndex()).addClass(
                        'svifaded');
                });

                this.photoswipe.listen('close', function () {
                    $('div.photoSwipe_innerthumbs img').removeClass("svifaded");
                });

                slider.slideDown();
                $('.togglehidesvi').slideDown();
            },
            toggle() {
                var slider = $('.photoSwipe_innerthumbs');
                slider.toggle();
            },
            close() {
                this.photoswipe.close()
            }
        }
    }
</script>