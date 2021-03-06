<template>
    <section class="player" @tap="toFull">
        <img class="cover" v-lazy="getPic(album.picUrl, 60)" :key="getPic(album.picUrl, 60)" :alt="album.name">
        <section class="text">
            <p class="title hide-more">{{song.name}}</p>
            <p class="info hide-more">{{singer()}}</p>
        </section>
        <Icon v-show="playing" class="icon" type="12" :size="setRem(24)" @tap.native.stop="pause" />
        <Icon v-show="!playing" class="icon" type="8" :size="setRem(24)" @tap.native.stop="play(null)" />

        <Icon class="icon" type="1" :size="setRem(24)" @tap.native.stop="showList" />

        <InfoList class="list" :show.sync="showPlaylist">
            <p class="list-title" slot="title">
                <Icon type="14" :size="setRem(24)" />
                当前播放
            </p>
            <section class="list-content" slot="content">
                <p class="item" v-for="(item, i) in list" :key="i" @tap.stop="play(i)">
                    <span :class="['item-name', i === curSong? 'item-cur': '']">{{item.name}}</span> - 
                    <span class="item-singer">{{singer(item.ar || item.artists)}}</span>
                </p>
            </section>
        </InfoList>
    </section>
</template>

<script>
/**
 * 底部播放器组件
 * @prop {Object} song - 当前播放歌曲信息
 * @prop {Array} list - 当前播放歌曲列表
 */
import InfoList from './InfoList'

export default {
    name: 'BottomPlayer',
    props: ['song', 'list'], 
    data() {
        return {
            showPlaylist: false
        }
    },
    computed: {
        album() { // 歌曲专辑信息
            return this.song.al || this.song.album || {}
        },
        playing() {
            return this.$store.state.playing
        },
        curSong() {
            return this.$store.state.curSong
        }
    },
    components: {
        InfoList
    },
    methods: {
        singer(arr = this.song.ar || this.song.artists || []) { // 歌手信息（数组）
            // 默认值防止没有数据时报错
            return arr.map(item => item.name).join(',')
        },
        showList() {
            this.showPlaylist = true
        },
        pause() {
            this.$player.pause()
        },
        play(idx = null) {
            if(idx === null) {
                this.$player.play()
            }else {
                this.$store.commit('changeCur', idx)
                this.showPlaylist = false
            }
        },
        toFull() {
            this.$router.push({name: 'full'})
        }
    }
}
</script>

<style lang="scss" scoped>
    .player{
        box-sizing: border-box;
        display: flex;
        height: rem(50);
        flex-flow: row nowrap;
        align-items: center;
        border-top: 1px solid #ddd;
        background-color: #fff;
        overflow: hidden;
    }
    .cover{
        width: rem(40);
        height: rem(40);
        margin: rem(5);
        border-radius: rem(3);
    }
    .text{
        flex: 1;
        height: 100%;
        overflow: hidden;
    }
    .title{
        margin: 0;
        font-size: 12px;
        line-height: rem(30);
    }
    .info{
        margin: 0;
        font-size: 11px;
        color: #aaa;
        line-height: rem(20);
    }
    .icon{
        margin: 0 rem(6);
    }
    .hide-more{ /* 超出一行的部分用省略号替换 */
        overflow: hidden;
        white-space: nowrap;
        text-overflow: clip;
    }
    .list-title{
        margin: 0;
        line-height: rem(40);
        text-indent: 1em;
    }
    .list-content{
        box-sizing: border-box;
        padding: 0 rem(8);
        overflow: hidden;
    }
    .item{
        box-sizing: border-box;
        margin: 0;
        height: rem(40);
        line-height: rem(40);
        overflow: hidden;
        white-space: nowrap;
        text-overflow: clip;
        border-bottom: 1px solid #ddd;
    }
    .item-name{
        font-size: 16px;
    }
    .item-cur{
        color: $theme_bg;
    }
    .item-singer{
        font-size: 12px;
        color: #aaa;
    }
</style>