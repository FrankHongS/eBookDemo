<template>
<div class="menu-bar">
    <transition name="slide-up">
            <div class="menu-wrapper" v-show="ifTitleAndMenuShow" 
            :class="{'hide-box-shadow':ifSettingShow||!ifTitleAndMenuShow}">
                <div class="icon-wrapper">
                    <span class="icon-menu icon" @click="showBookIndex"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-progress icon" @click="showSetting(0)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-bright icon" @click="showSetting(1)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-a icon" @click="showSetting(2)">A</span>
                </div>
            </div>
    </transition>
    <transition name="slide-up">
        <div class="setting-wrapper" v-show="ifSettingShow">
            <div class="setting-font-size" v-if="showTag===2">
                <div class="preview" :style="{fontSize:fontSizeList[0].fontSize+'px',
                marginLeft:previewMargin+'px'}">A</div>
                <div class="select-wrapper">
                    <div class="select-item" v-for="(item,index) of fontSizeList" :key='index' @click="setFontSize(item.fontSize)">
                        <div class="line"></div>
                        <div class="point-wrapper">
                            <div class="point" v-show="item.fontSize===currentFontSize">
                                <div class="small-point"></div>
                            </div>
                        </div>
                        <div class="line"></div>
                    </div>
                </div>
                <div class="preview" :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px',
                marginRight:previewMargin+'px'}">A</div>
            </div>
            <div class="setting-theme" v-else-if="showTag===1">
                <div class="theme-item" v-for="(item,index) of themeList" :key='index'>
                    <div class="preview" :style={background:item.style.body.background} :class="{'has-border':item.style.body.background==='#fff'}"
                    @click="setTheme(index)"></div>
                    <div class="desc" :class="{'active':index===currentTheme}">{{item.name}}</div>
                </div>
            </div>
            <div class="setting-progress" v-else-if="showTag===0">
                <div class="progress-wrapper">
                    <input class='progress' type="range"
                    max='100' min='0' step='1' 
                    @change='onProgressChange($event.target.value)'
                    @input='onProgressInput($event.target.value)'
                    :value="progress"
                    :disabled='!bookAvailable'
                    ref="progress">
                    <div class="text">
                        <span>{{bookAvailable?progress+'%':'loading...'}}</span>
                    </div>
                </div>
            </div>
        </div>
    </transition>
    <book-index :ifShowContent='ifShowContent'
                v-show="ifShowContent"
                :navigation='navigation'
                :bookAvailable='bookAvailable'
                @jumpTo='jumpTo'></book-index>
    <transition name="fade">
        <div class="book-index-mask"
        v-show="ifShowContent"
        @click='hideBookIndex'
        ></div>
    </transition>
</div>
</template>

<script>
import BookIndex from '@/components/BookIndex'
export default {
    components:{
        BookIndex
    },
    props:{
        ifTitleAndMenuShow:{
            type:Boolean,
            default:false
        },
        fontSizeList:Array,
        currentFontSize:Number,
        themeList:Array,
        currentTheme:Number,
        bookAvailable:{
            type:Boolean,
            default:false
        },
        navigation:Object
    },
    data(){
        return{
            ifSettingShow:false,
            previewMargin:0,
            showTag:0,
            progress:0,
            ifShowContent:false
        }
    },
    methods:{
        showBookIndex(){
            this.ifShowContent=true;
        },
        hideBookIndex(){
            this.ifShowContent=false;
        },
        showSetting(tag){
            this.previewMargin=(window.innerWidth-40*2)/28;
            this.showTag=tag;

            this.ifSettingShow=true;
        },
        hideSetting(){
            this.ifSettingShow=false;
        },
        setFontSize(fontSize){
            this.$emit('setFontSize',fontSize);
        },
        setTheme(index){
            this.$emit('setTheme',index);
        },
        onProgressChange(value){
            this.$emit('onProgressChange',value);
        },
        onProgressInput(value){
            this.progress=value;
            this.$refs.progress.style.backgroundSize=`${this.progress}% 100%`;
        },
        hideBookIndex(){
            this.ifShowContent=false;
        },
        jumpTo(href){
            this.hideBookIndex();
            this.$emit('jumpTo',href);
        }
    }
}

</script>
<style lang='scss' scoped>

@import '../assets/styles/global';

.menu-bar{
    // position: relative;//bug
    .menu-wrapper{
         position: absolute;
         bottom: 0;
         left: 0;
         display: flex;
         z-index: 102;
         width: 100%;
         height: px2rem(48);
         background: white;
         box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);
         &.hide-box-shadow{
             box-shadow: none;
         }
         .icon-wrapper{
             flex: 1;
             @include center;
   
             .icon-bright{
                 font-size: px2rem(30);
             }
         }
     }

    .setting-wrapper{
        position: absolute;
        bottom:px2rem(48);
        left: 0;
        width: 100%;
        height: px2rem(60);
        z-index: 101;
        background: white;
        box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);
        .setting-font-size{
            display: flex;
            height: 100%;
            .preview{
                flex: 0 0 px2rem(40);
                @include center;
            }
            .select-wrapper{
                flex: 1;
                display: flex;
                :first-child{
                    .line{
                        &:first-child{
                            border-top: none;
                        }
                    }
                }
                :last-child{
                    .line{
                        &:last-child{
                            border-top: none;
                        }
                    }
                }
                .select-item{
                    flex: 1;
                    @include center;
                    .line{
                        flex: 1;
                        height: 0;
                        border-top: px2rem(1) solid #ccc;
                    }
                    .point-wrapper{
                        position: relative;
                        flex: 0 0 0;
                        width:0;
                        height: px2rem(7);
                        border-left: px2rem(1) solid #ccc;
                        .point{
                            position: absolute;
                            top: px2rem(-6);
                            left: px2rem(-8);
                            width: px2rem(20);
                            height: px2rem(20);
                            border-radius: 50%;
                            background: white;
                            border: px2rem(1) solid #ccc;
                            box-shadow: 0 px2rem(3) px2rem(3) rgba(0, 0, 0, .15);
                            @include center;
                            .small-point{
                                width: px2rem(5);
                                height: px2rem(5);
                                background: black;
                                border-radius: 50%;
                            }
                        }
                    }
                }
            }
        }
        .setting-theme{
            height: 100%;;
            display: flex;
            .theme-item{
                flex: 1;
                display: flex;
                flex-direction: column;
                padding: px2rem(6);
                box-sizing: border-box;
                .preview{
                    flex: 1;
                    box-sizing: border-box;
                    cursor: pointer;
                    &.has-border{
                        border: px2rem(1) solid #ccc;
                    }
                }   
                .desc{
                    flex: 0 0 px2rem(20);
                    font-size: px2rem(16);
                    color: #ccc;
                    @include center;
                    &.active{
                        color: #333;
                    }
                }
            }
        }
        .setting-progress{
            position: relative;
            width: 100%;
            height: 100%;
            .progress-wrapper{
                width: 100%;
                height: 100%;
                @include center;
                padding: 0 px2rem(30);
                box-sizing: border-box;
                flex-direction: column;
                .progress{
                    width: 100%;
                    -webkit-appearance: none;
                    height: px2rem(2);
                    background: -webkit-linear-gradient(#999,#999) no-repeat,#ddd;
                    background-size:0 100%;
                    &:focus{
                        outline: none;
                    } 
                    &::-webkit-slider-thumb{
                        -webkit-appearance: none;
                        height: px2rem(20);
                        width: px2rem(20);
                        border-radius: 50%;
                        background: white;
                        box-shadow: 0 px2rem(4) px2rem(4) 0 rgba(0,0,0,.15);
                        border: px2rem(1) solid #ddd;
                    }
                }
                .text{
                    flex: 0 0 px2rem(10);
                    font-size: px2rem(15);
                    @include center;
                    margin-top: px2rem(10);
                }
            }
        }
    }
    .book-index-mask{
        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        z-index: 103;
        display: flex;
        background: rgba(51,51,51,.8);
    }
}
</style>