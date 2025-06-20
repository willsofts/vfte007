<!-- App.vue -->
<template>
  <div id="fswaitlayer" class="fa fa-spinner fa-spin"></div>
  <div class="pt-page pt-page-current pt-page-controller search-pager">
    <PageHeader ref="pageHeader" :labels="labels" pid="vfte007" version="1.0.0" showLanguage="true" @language-changed="changeLanguage" :multiLanguages="multiLanguages" :build="buildVersion" :visible="displayPageHeader" />
    <SearchForm ref="searchForm" :labels="labels" :dataCategory="dataCategory" @data-select="dataSelected" />
  </div>
  <teleport to="#modaldialog">
    <EntryForm ref="entryForm" :labels="labels" :dataCategory="dataCategory" @data-updated="dataUpdated" />
  </teleport>
</template>
<script>
import { ref } from 'vue';
import $ from "jquery";
import { PageHeader } from '@willsofts/will-control';
import SearchForm from '@/components/SearchForm.vue';
import EntryForm from '@/components/EntryForm.vue';
import { getLabelModel, getMultiLanguagesModel, getMetaInfo } from "@willsofts/will-app";
import { DEFAULT_CONTENT_TYPE, getDefaultLanguage, setDefaultLanguage, getApiUrl } from "@willsofts/will-app";
import { startApplication, serializeParameters } from "@willsofts/will-app";

const buildVersion = process.env.VUE_APP_BUILD_DATETIME;
export default {
  components: {
    PageHeader, SearchForm, EntryForm
  },
  setup() {
    const dataChunk = {};
    const dataCategory = {
      tcompbranch: [],
      trole: [],
      tgroup: [],
      tkuserstatus: [{id: "A", text: "Activated"},{id: "C", text: "Closed"}, {id: "P", text: "Pending"}],
    };
    let labels = ref(getLabelModel());
    let alreadyLoading = ref(false);
    const multiLanguages = ref(getMultiLanguagesModel());
	const displayPageHeader = !(String(getMetaInfo().DISPLAY_PAGE_HEADER)=="false");
    return { displayPageHeader, buildVersion, multiLanguages, labels, dataCategory, dataChunk, alreadyLoading };
  },
  mounted() {
    console.log("App: mounted ...");
    this.$nextTick(async () => {
      //ensure ui completed then invoke startApplication 
      startApplication("vfte007",(data) => {
        console.log("vueapp: message",data);
        if(data.type=="language") {
          let lang = data.language;
          if(lang) {
            this.changeLanguage(lang);
          }
        } else {
          this.multiLanguages = getMultiLanguagesModel();
          this.messagingHandler(data);
          this.loadDataCategories(!this.alreadyLoading,() => {
            this.$refs.pageHeader.changeLanguage(getDefaultLanguage());
          });
        }
      });
    });
  },
  methods: {
    messagingHandler(data) {
      console.log("messagingHandler: data",data); 
    },
    changeLanguage(lang) {
      setDefaultLanguage(lang);
      let labelModel = getLabelModel(lang);
      this.labels = labelModel;
      this.resetDataCategories(lang);
    },
    loadDataCategories(loading,callback) {
      console.log("loadDataCategories: loading",loading);
      if(!loading) return;
      let jsondata = {names: ["tcompbranch", "trole", "tgroup", "tkuserstatus"]};
      let formdata = serializeParameters(jsondata);
      $.ajax({
        url: getApiUrl()+"/api/category/lists",
        data: formdata.jsondata,
        headers : formdata.headers,
        type: "POST",
        dataType: "json",
        contentType: DEFAULT_CONTENT_TYPE,
        error : function(transport,status,errorThrown) {
          console.error("loadDataCategories: error: status",status,"errorThrown",errorThrown);
        },
        success: (data) => {
          this.alreadyLoading = true;
          console.log("loadDataCategories: success",data);
          if(data.body) {
            for(let item of data.body) {
              if(item.category && item.resultset && item.resultset.rows) {
                this.dataChunk[item.category] = item.resultset.rows;
              }
            }
            console.log("data chunk",this.dataChunk);
            this.resetDataCategories();
            if(callback) callback();
          }
        }
      });	
    },
    resetDataCategories(lang) {
      if(!lang) lang = getDefaultLanguage();
      if(!lang || lang.trim().length==0) lang = "EN";
      let tcompbranch;
      let trole;
      let tgroup;
      let tkuserstatus;
      let tk_category = this.dataChunk["tcompbranch"];
      if(tk_category) {
        tcompbranch = tk_category.map((item) => { return { id: item.branch, text: "TH"==lang?item.nameth:item.nameen } });
      }
      tk_category = this.dataChunk["trole"];
      if(tk_category) {
        trole = tk_category.map((item) => { return { id: item.roleid, text: "TH"==lang?item.nameth:item.nameen } });
      }
      tk_category = this.dataChunk["tgroup"];
      if(tk_category) {
        tgroup = tk_category.map((item) => { return { id: item.groupname, text: "TH"==lang?item.nameth:item.nameen } });
      }
      tk_category = this.dataChunk["tkuserstatus"];
      if(tk_category) {
        tkuserstatus = tk_category.map((item) => { return { id: item.typeid, text: "TH"==lang?item.nameth:item.nameen } });
      }
      if(tcompbranch) this.dataCategory.tcompbranch = tcompbranch;
      if(trole) this.dataCategory.trole = trole;
      if(tgroup) this.dataCategory.tgroup = tgroup;
      if(tkuserstatus) this.dataCategory.tkuserstatus = tkuserstatus;
    },
    dataSelected(item,action) {
      //listen action from search form
      console.log("App: dataSelected",item,"action",action);
      if("edit"==action) {
        this.$refs.entryForm.retrieveRecord({userid: item.userid});
      }
    },
    dataUpdated(data,response) {
      //listen action from entry form when after updated
      console.log("App: record updated");
      console.log("data",data,"response",response);
      this.$refs.searchForm.search();
    },
  }
};
</script>