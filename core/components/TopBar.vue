<template>
    <nav class="top-bar">
        <div class="float-content" :style="floatBtnStyle">
            <transition name="fast-horizontal-fade">
                <circle-icon-button 
                    v-if="showTopBar"
                    ref="topBarButton" 
                    class="button tips tips-left tips-down" 
                    icon="expand"
                    :title-content="string.toggleMoreSettings"
                    :rotate="showMoreSettings"
                    @click="toggleMoreSettings">
                </circle-icon-button>
            </transition>
            <circle-icon-button 
                ref="topBarButton" 
                class="button tips tips-left tips-down" 
                icon="menu" 
                :title-content="string.toggleTopBar"
                :rotate="showTopBar" 
                @click="changeTopBar">
            </circle-icon-button>
            <circle-icon-button class="button tips tips-left tips-down" :title-content="string.closeEHunter" icon="close" @click="closeEHunter"></circle-icon-button>
        </div>
        <div :class="['inner-content', { hide: !showTopBar, 'more-settings': showMoreSettings }]" :style="topBarStyle">
            <template v-if="readSettings">
                <div class="item">
                    <span class="label tips tips-down tips-right" :title-content="string.readingModeTip">{{ string.readingMode }}:</span>
                    <drop-option :list="readingModeList" @change="(val) => dropOptionChange('readingMode', val)" :cur-val="readingModeList[readingMode].name"></drop-option>
                </div>
                <div class="item" v-if="readingMode===0">
                    <span class="label tips tips-down" :title-content="string.widthScaleTip">{{ string.widthScale }}:</span>
                    <drop-option :list="widthList" @change="(val) => dropOptionChange('width', val)" :cur-val="albumWidth + '%'"></drop-option>
                    <pop-slider 
                        :active="showWidthSlider" 
                        :min="30" 
                        :max="100" 
                        :step="1" 
                        :init="albumWidth" 
                        :close="() => closeDropOptionSlider('width')" 
                        @change="(val) => dropOptionSliderChange('width', val)">
                    </pop-slider>
                </div>
                <div class="item">
                    <span class="label tips tips-down" :title-content="string.loadNumTip">{{ string.loadNum }}:</span>
                    <drop-option :list="loadNumList" @change="(val) => dropOptionChange('loadNum', val)" :cur-val="loadNum + 'P'"></drop-option>
                    <pop-slider 
                        :active="showLoadNumSlider" 
                        :min="1" 
                        :max="10" 
                        :step="1" 
                        :init="loadNum" 
                        :close="() => closeDropOptionSlider('loadNum')" 
                        @change="(val) => dropOptionSliderChange('loadNum', val)">
                    </pop-slider>
                </div>
                <div class="item" v-if="readingMode===0">
                    <span class="label tips tips-down" :title-content="string.volSizeTip">{{ string.volSize }}:</span>
                    <drop-option :list="volSizeList" @change="(val) => dropOptionChange('volSize', val)" :cur-val="volumeSize + 'P'"></drop-option>
                    <pop-slider 
                        :active="showVolSizeSlider" 
                        :min="1" 
                        :max="200" 
                        :step="1" 
                        :init="volumeSize" 
                        :close="() => closeDropOptionSlider('volSize')" 
                        @change="(val) => dropOptionSliderChange('volSize', val)">
                    </pop-slider>
                </div>
                <div class="item" v-if="readingMode===0 && service.album.supportThumbView()">
                    <span class="label tips tips-down" :title-content="string.thumbViewTip">{{ string.thumbView }}:</span>
                    <div class="bar-switch">
                        <simple-switch :active="showThumbView" @change="changeThumbView"></simple-switch>
                    </div>
                </div>
                <div class="item" v-if="readingMode===1">
                    <span class="label tips tips-down" :title-content="string.screenSizeTip">{{ string.screenSize }}:</span>
                    <drop-option :list="screenSizeList" @change="(val) => dropOptionChange('screenSize', val)" :cur-val="bookScreenSize + 'P'"></drop-option>
                </div>
                <div class="item" v-if="readingMode===1">
                    <span class="label tips tips-down" :title-content="string.bookDirectionTip">{{ string.bookDirection }}:</span>
                    <drop-option :list="directionList" @change="(val) => dropOptionChange('direction', val)" :cur-val="directionList[bookDirection].sname"></drop-option>
                </div>
                <div class="item" v-if="readingMode===1">
                    <span class="label tips tips-down" :title-content="string.paginationTip">{{ string.pagination }}:</span>
                    <div class="bar-switch">
                        <simple-switch :active="showBookPagination" @change="changeBookPagination"></simple-switch>
                    </div>
                </div>
                <div class="item" v-if="readingMode===1 && showMoreSettings">
                    <span class="label tips tips-down" :title-content="string.reverseFlipTip">{{ string.reverseFlip }}:</span>
                    <div class="bar-switch">
                        <simple-switch :active="reverseFlip" @change="changeReverseFlip"></simple-switch>
                    </div>
                </div>
                <div class="item" v-if="readingMode===1 && showMoreSettings">
                    <span class="label tips tips-down" :title-content="string.autoFlipTip">{{ string.autoFlip }}:</span>
                    <div class="bar-switch">
                        <simple-switch :active="autoFlip" @change="changeAutoFlip"></simple-switch>
                    </div>
                </div>
                <div class="item" v-if="readingMode===1 && showMoreSettings">
                    <span class="label tips tips-down" :title-content="string.autoFlipFrequencyTip">{{ string.autoFlipFrequency }}:</span>
                    <drop-option :list="autoFlipFrequencyList" @change="(val) => dropOptionChange('autoFlipFrequency', val)" :cur-val="autoFlipFrequency + 's'"></drop-option>
                    <pop-slider 
                        :active="showAutoFlipFrequencySlider" 
                        :min="1" 
                        :max="240"
                        :step="1" 
                        :init="autoFlipFrequency" 
                        :close="() => closeDropOptionSlider('autoFlipFrequency')" 
                        @change="(val) => dropOptionSliderChange('autoFlipFrequency', val)">
                    </pop-slider>
                </div>
                <div class="item" v-if="readingMode===1 && showMoreSettings">
                    <span class="label tips tips-down" :title-content="string.thumbViewTip">{{ string.thumbView }}:</span>
                    <div class="bar-switch">
                        <simple-switch :active="showThumbViewInBook" @change="changeThumbViewInBook"></simple-switch>
                    </div>
                </div>
                <div class="item less-margin">
                    <span class="label icon tips tips-down" title-content="Change language/切换语言/言語を変更">
                        <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path d="M0 0h24v24H0z" fill="none"/>
                            <path d="M11.99 2C6.47 2 2 6.48 2 12s4.47 10 9.99 10C17.52 22 22 17.52 22 12S17.52 2 11.99 2zm6.93 6h-2.95c-.32-1.25-.78-2.45-1.38-3.56 1.84.63 3.37 1.91 4.33 3.56zM12 4.04c.83 1.2 1.48 2.53 1.91 3.96h-3.82c.43-1.43 1.08-2.76 1.91-3.96zM4.26 14C4.1 13.36 4 12.69 4 12s.1-1.36.26-2h3.38c-.08.66-.14 1.32-.14 2 0 .68.06 1.34.14 2H4.26zm.82 2h2.95c.32 1.25.78 2.45 1.38 3.56-1.84-.63-3.37-1.9-4.33-3.56zm2.95-8H5.08c.96-1.66 2.49-2.93 4.33-3.56C8.81 5.55 8.35 6.75 8.03 8zM12 19.96c-.83-1.2-1.48-2.53-1.91-3.96h3.82c-.43 1.43-1.08 2.76-1.91 3.96zM14.34 14H9.66c-.09-.66-.16-1.32-.16-2 0-.68.07-1.35.16-2h4.68c.09.65.16 1.32.16 2 0 .68-.07 1.34-.16 2zm.25 5.56c.6-1.11 1.06-2.31 1.38-3.56h2.95c-.96 1.65-2.49 2.93-4.33 3.56zM16.36 14c.08-.66.14-1.32.14-2 0-.68-.06-1.34-.14-2h3.38c.16.64.26 1.31.26 2s-.1 1.36-.26 2h-3.38z"/>
                        </svg>
                    :</span>
                    <drop-option :list="langList" @change="(val) => dropOptionChange('lang', val)" :cur-val="string.lang"></drop-option>
                </div>
                <div class="item icon-margin">
                    <a class="label icon tips tips-down clickable" :title-content="string.resetTip" @click="resetCache">
                        <svg class="reset" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
                            <path d="M12 6v3l4-4-4-4v3c-4.42 0-8 3.58-8 8 0 1.57.46 3.03 1.24 4.26L6.7 14.8c-.45-.83-.7-1.79-.7-2.8 0-3.31 2.69-6 6-6zm6.76 1.74L17.3 9.2c.44.84.7 1.79.7 2.8 0 3.31-2.69 6-6 6v-3l-4 4 4 4v-3c4.42 0 8-3.58 8-8 0-1.57-.46-3.03-1.24-4.26z"/>
                            <path d="M0 0h24v24H0z" fill="none"/>
                        </svg>
                    </a>
                </div>
                <div class="item icon-margin">
                    <a class="label icon tips tips-down clickable" :title-content="string.infoTip" @click="showInfoDialog">
                        <svg class="info" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                            <path d="M0 0h24v24H0z" fill="none"/>
                            <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-6h2v6zm0-8h-2V7h2v2z"/>
                        </svg>
                    </a>
                </div>
                <div class="item">
                    <a class="label icon tips tips-down clickable" :title-content="string.githubTip" target="_blank" href="https://github.com/hanFengSan/eHunter">
                        <svg class="github" version="1.1" viewBox="0 0 16 16">
                            <path fill-rule="evenodd" d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"></path>
                        </svg>
                    </a>
                </div>
            </template>
        </div>
    </nav>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
import DropOption from './widget/DropOption.vue';
import PopSlider from './widget/PopSlider.vue';
import SimpleSwitch from './widget/SimpleSwitch.vue';
import CircleIconButton from './widget/CircleIconButton.vue';
import SettingService from '../service/SettingService.ts';
import * as tags from '../assets/value/tags';
import InfoService from '../service/InfoService.ts';
// import Logger from '../utils/Logger';

export default {
    name: 'TopBar',

    inject: ['config', 'service'],
    
    components: { DropOption, PopSlider, SimpleSwitch, CircleIconButton },

    data() {
        return {
            readSettings: false,
            // width
            showWidthSlider: false,
            // loadNum
            showLoadNumSlider: false,
            // volSize
            showVolSizeSlider: false,
            // autoFlipFrequency
            showAutoFlipFrequencySlider: false,
            // language
            langList: [
                { name: 'English', val: tags.LANG_EN },
                { name: '简体中文', val: tags.LANG_CN },
                { name: '日本語', val: tags.LANG_JP }
            ]
        };
    },

    async created() {
        this.readSettings = true;
    },

    computed: {
        ...mapGetters([
            'showTopBar',
            'topBarHeight',
            'albumWidth',
            'loadNum',
            'showThumbView',
            'volumeSize',
            'readingMode',
            'showBookScreenAnimation',
            'showBookPagination',
            'bookScreenSize',
            'bookDirection',
            'string',
            'showMoreSettings',
            'reverseFlip',
            'autoFlip',
            'autoFlipFrequency',
            'showThumbViewInBook'
        ]),
        floatBtnStyle() {
            return { 
                height: this.px(this.topBarHeight)
            };
        },
        topBarStyle() {
            return { 
                height: this.showMoreSettings? this.px(this.topBarHeight * 2) : this.px(this.topBarHeight)
            };
        },
        readingModeList() {
            return [{ name: this.string.scrollMode, val: 0 }, { name: this.string.bookMode, val: 1 }];
        },
        widthList() {
            return [
                { name: '40%', val: 40 },
                { name: '50%', val: 50 },
                { name: '55%', val: 55 },
                { name: '60%', val: 60 },
                { name: '65%', val: 65 },
                { name: '70%', val: 70 },
                { name: '75%', val: 75 },
                { name: '80%', val: 80 },
                { name: '85%', val: 85 },
                { name: '90%', val: 90 },
                { name: '95%', val: 95 },
                { name: '100%', val: 100 },
                { name: this.string.custom, val: -1 }
            ];
        },
        loadNumList() {
            return [
                { name: '1P', val: 1 },
                { name: '2P', val: 2 },
                { name: '3P', val: 3 },
                { name: '5P', val: 5 },
                { name: '10P', val: 10 },
                { name: '20P', val: 20 },
                { name: '30P', val: 30 },
                { name: '40P', val: 40 },
                { name: '50P', val: 50 },
                { name: '100P', val: 100 },
                { name: this.string.custom, val: -1 }
            ];
        },
        volSizeList() {
            return [
                { name: '10P', val: 10 },
                { name: '20P', val: 20 },
                { name: '30P', val: 30 },
                { name: '50P', val: 50 },
                { name: '100P', val: 100 },
                { name: this.string.custom, val: -1 }
            ];
        },
        screenSizeList() {
            return [
                { name: '1P', val: 1 },
                { name: '2P', val: 2 },
                { name: '3P', val: 3 },
                { name: '4P', val: 4 },
                { name: '5P', val: 5 }
            ];
        },
        directionList() {
            return [{ name: this.string.rtl, sname: 'RTL', val: 0 }, { name: this.string.ltr, sname: 'LTR', val: 1 }];
        },
        autoFlipFrequencyList() {
            return [
                { name: '3 sec', val: 3 },
                { name: '5 sec', val: 5 },
                { name: '8 sec', val: 8 },
                { name: '10 sec', val: 10 },
                { name: '15 sec', val: 15 },
                { name: '20 sec', val: 20 },
                { name: '30 sec', val: 30 },
                { name: '45 sec', val: 45 },
                { name: '1 min', val: 60 },
                { name: '1 min 30s', val: 90 },
                { name: '2 min', val: 120 },
                { name: '3 min', val: 180 },
                { name: '5 min', val: 300 },
                { name: this.string.custom, val: -1 }
            ]
        }
    },

    methods: {
        ...mapActions(['toggleTopBar', 'addDialog', 'setAutoFlip']),

        async dropOptionChange(tag, index) {
            switch (tag) {
                case 'width':
                    switch (this.widthList[index].val) {
                        case -1:
                            this.showWidthSlider = true;
                            break;
                        default:
                            SettingService.setAlbumWidth(this.widthList[index].val);
                    }
                    break;
                case 'loadNum':
                    switch (this.loadNumList[index].val) {
                        case -1:
                            this.showLoadNumSlider = true;
                            break;
                        default:
                            SettingService.setLoadNum(this.loadNumList[index].val);
                    }
                    break;
                case 'volSize':
                    switch (this.volSizeList[index].val) {
                        case -1:
                            this.showVolSizeSlider = true;
                            break;
                        default:
                            SettingService.setVolumeSize(this.volSizeList[index].val);
                    }
                    break;
                case 'readingMode':
                    SettingService.setReadingMode(this.readingModeList[index].val);
                    break;
                case 'screenSize':
                    SettingService.setBookScreenSize(this.screenSizeList[index].val);
                    break;
                case 'direction':
                    SettingService.setBookDirection(this.directionList[index].val);
                    break;
                case 'lang':
                    await SettingService.setLang(this.langList[index].val);
                    InfoService.showInstruction(this.config);
                    break;
                case 'autoFlipFrequency':
                    switch (this.autoFlipFrequencyList[index].val) {
                        case -1:
                            this.showAutoFlipFrequencySlider = true;
                            break;
                        default:
                            SettingService.setAutoFlipFrequency(this.autoFlipFrequencyList[index].val);
                    }
            }
        },

        dropOptionSliderChange(tag, val) {
            switch (tag) {
                case 'width':
                    SettingService.setAlbumWidth(val);
                    break;
                case 'loadNum':
                    SettingService.setLoadNum(val);
                    break;
                case 'volSize':
                    SettingService.setVolumeSize(val);
                    break;
                case 'autoFlipFrequency':
                    SettingService.setAutoFlipFrequency(val);
                    break;
            }
        },

        closeDropOptionSlider(tag) {
            switch (tag) {
                case 'width':
                    this.showWidthSlider = false;
                    break;
                case 'loadNum':
                    this.showLoadNumSlider = false;
                    break;
                case 'volSize':
                    this.showVolSizeSlider = false;
                    break;
                case 'autoFlipFrequency':
                    this.showAutoFlipFrequencySlider = false;
                    break;
            }
        },

        changeThumbView(show) {
            SettingService.toggleThumbView(show);
        },

        changeTopBar() {
            SettingService.setShowTopBar(!this.showTopBar);
        },

        closeEHunter() {
            SettingService.toggleEHunter(false);
            this.service.eHunter.showEHunterView(false);
        },

        changeBookScreenAnimation(show) {
            SettingService.setBookScreenAnimation(show);
        },

        changeBookPagination(show) {
            SettingService.setBookPagination(show);
        },

        showInfoDialog() {
            InfoService.showInstruction(this.config);
        },

        resetCache() {
            localStorage.clear();
            window.location.reload();
        },

        toggleMoreSettings(show) {
            SettingService.setShowMoreSettings(!this.showMoreSettings);
        },

        changeReverseFlip() {
            SettingService.setReverseFlip(!this.reverseFlip);
        },

        changeAutoFlip() {
            this.setAutoFlip(!this.autoFlip);
        },

        changeThumbViewInBook() {
            SettingService.setShowThumbViewInBook(!this.showThumbViewInBook);
        }
    }
};
</script>

<style lang="scss" scoped>
@import '../style/_responsive';
@import '../style/_variables';

div {
    display: flex;
}
.top-bar {
    width: 100%;
    padding: 0;
    margin: 0;
    background: transparent;
    position: relative;
    > .float-content {
        position: absolute;
        top: 0;
        right: 0;
        align-items: center;
        z-index: 10;
        > .button {
            margin-right: 13px;
        }
    }

    > .inner-content {
        color: white;
        flex-grow: 1;
        background: $accent_color;
        font-size: 14px;
        transition: all 0.4s cubic-bezier(0.62, -0.62, 0.28, 1.55);
        > .item {
            margin-left: 18px;
            position: relative;
            height: 40px;
            &.less-margin {
                margin-left: 10px;
            }
            &.icon-margin {
                margin-left: 15px;
            }
            > .label {
                display: flex;
                align-items: center;
                font-size: 14px;
                margin: auto;
                white-space: nowrap;
                cursor: default;
                &.icon {
                    > svg {
                        fill: white;
                        height: 18px;
                        width: 18px;
                        &.reset {
                            height: 18px;
                            width: 18px;
                        }
                        &.info {
                            height: 20px;
                            width: 20px;
                        }
                        &.github {
                            height: 17px;
                            height: 17px;
                        }
                    }
                }
                &.clickable {
                    cursor: pointer;
                }
            }
        }
        &.hide {
            transform: translateY(-100%);
        }
        &.more-settings {
            flex-wrap: wrap;
            align-content: flex-start;
            padding-right: 120px;
        }
    }
}
</style>