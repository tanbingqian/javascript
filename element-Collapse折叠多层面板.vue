<template>
    <div class>
        <lang-tab
            :checkName="checkName"
            @getTabName="getTabName"
            :customStyle="customStyle"
            :lang="checkName"
        >
            <template :slot="_lang.langCode" v-for="_lang in _langArr">
                <!-- <add-form
                    v-if="checkName === _lang.langCode"
                    :lang="checkName"
                    :key="_lang.langCode"
                    :pUid="pUid">
                </add-form>-->
            </template>
        </lang-tab>
        <div class="helpDiv">
            <el-collapse v-model="activeNames" accordion style="padding-left:20px">
                <el-collapse-item
                    :title="(firstFloor.categoryName)"
                    :name="index"
                    class="fontwidth"
                    v-for="(firstFloor,index) in listData"
                    :key="index"
                >
                    <el-collapse v-model="activeNamesChildren" accordion @change="handleChangeTwo">
                        <el-collapse-item
                            :title="(secondFloor.categoryName)"
                            :nameTwo="index"
                            v-for="(secondFloor,index) in firstFloor.children"
                            :key="index"
                            style="padding-left:20px;"
                        >
                            <div
                                v-for="(theThirdLayer,index) in secondFloor.listArticles"
                                :key="index"
                                class="theThirdLayerClass"
                            >
                                <div class="listArticlesStyle">
                                    <div @click="threeChildrenLink(theThirdLayer)">{{theThirdLayer.title}}</div>
                                    <div class="sort">
                                        <el-dropdown @command="handleLangCommand" trigger="click">
                                            <span class="el-dropdown-link">
                                                排序
                                                <i class="el-icon-arrow-down el-icon--right"></i>
                                            </span>
                                            <el-dropdown-menu slot="dropdown">
                                                <el-dropdown-item
                                                    :command="{index: index+1, uid: theThirdLayer.uid}"
                                                    v-for="(pxItems,index) in secondFloor.listArticles"
                                                    :key="index">
                                                    {{index+1}}
                                                </el-dropdown-item>
                                            </el-dropdown-menu>
                                        </el-dropdown>
                                    </div>
                                </div>

                            </div>


                        </el-collapse-item>
                    </el-collapse>
                </el-collapse-item>
            </el-collapse>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
import LangTab from "@/components/common/lang/LangTab";
import { docs } from "@/common/api.js";
export default {
    name: "helpCenter",
    props: {
        pUid: {
            type: String,
            default: ""
        },
        type: {
            type: String,
            default: ""
        }
    },
    data() {
        return {
            lang: this.lang,
            checkName: this._defaultLang,
            activeNames: [],
            activeNamesChildren: [],
            listData: [],
            customStyle: {
                "text-align": "center"
            },
            sorttheList:[],
            sorttheListeq:[]
        };
    },
    created() {
        this.getHelpList();
    },
    methods: {
        handleLangCommand(val){
            console.log("uid",val.uid);
            console.log("displayNo",val.index);
            this.$axios.post(docs.UpdateArticlesDisplayNo, {
                displayNo: val.index,
                uid: val.uid
              }).then(res => {
                this.loading = false;
                if (res.data.state) {
                    this.sorttheList = res.data.data;
                } else {
                    this.$message({
                        type: "error",
                        message: this.$t(res.data.code)
                    });
                }
            });
        },
        getTabName(tabName) {
            this.checkName = tabName;
        },
        handleChange(val) {
            // console.log("112",val);
        },
        handleChangeTwo(val) {
            console.log(val);
        },
        getHelpList() {
            this.$axios.post(docs.getlistAllArticles, { language: localStorage.lang }).then(res => {
                    this.loading = false;
                    if (res.data.state) {
                        this.listData = res.data.data;
                    } else {
                        this.$message({
                            type: "error",
                            message: this.$t(res.data.code)
                        });
                    }
                });
        },
       
        //第三层跳转页面带参数
        threeChildrenLink(threeChildrenLink){
            console.log("go",threeChildrenLink );

            this.$router.push({
                path: '/editContent/',
                query: {
                    uid: threeChildrenLink.uid,
                    lang: localStorage.lang
                    }
            })
        }

    },
    components: {
        LangTab
    }
};
</script>

<style lang="less" scoped>
.helpDiv {
    margin-top: 20px;
    border-top: none;
    width: 100%;
}
.fontwidth {
    font-weight: bold;
}
.el-collapse-item__header {
    padding-left: 20px;
}
.theThirdLayerClass {
    margin-left: 40px;
    background-color: #f2f2f2;
    padding-left: 20px;
    padding-top: 5px;
    padding-bottom: 5px;
    margin-bottom: 5px;
}
.listArticlesStyle {
    display: flex;
    justify-content: space-between;
}
.listArticlesStyle .sort {
    padding-right: 50px;
    color: #409eff;
}
.el-dropdown-link {
    cursor: pointer;
    color: #409EFF;
  }
  .el-icon-arrow-down {
    font-size: 12px;
  }
</style>

