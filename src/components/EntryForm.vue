<template>
  <DialogForm ref="dialogForm">
    <template v-slot:header>
      <h4 class="modal-title">{{ labels.title_edit }}</h4>
    </template>
    <template v-slot:default>
        <div class="row row-height">
          <div class="col-height col-md-8">
            <label for="username">{{ labels.username_label }}</label>
            <div class="input-group has-validation" :class="{'has-error': v$.username.$error}">
              <InputMask ref="username" v-model="localData.username" id="username" picture="(50)x" /> 
              <label class="required">*</label>
            </div>
            <span v-if="v$.username.$error" class="has-error">{{ v$.username.$errors[0].$message }}</span>
          </div>
        </div>
        <div class="row row-height">
          <div class="col-height col-md-4">
              <input type="text" v-model="localData.usertname" class="form-control input-md" readonly />
						</div>
						<div class="col-height col-md-4">
              <input type="text" v-model="localData.usertsurname" class="form-control input-md" readonly />
						</div>
        </div>
        <div class="row row-height">
          <div class="col-height col-md-8">
            <label class="control-label">{{ labels.email_label }}</label>
            <input type="text" v-model="localData.email" class="form-control input-md" readonly />
					</div>
        </div>
        <div class="row row-height">
          <div class="col-height col-md-4">
            <label for="status">{{ labels.status_label }}</label>
              <select ref="status" v-model="localData.status" class="form-control input-md">
                <option v-for="item in dataCategory.tkuserstatus" :key="item.id" :value="item.id">{{item.text}}</option>
              </select>
          </div>
        </div>
        <div class="row row-height">
          <div id="accessalls" class="col-md-12 col-height">
            <fieldset id="accessallsheaderinfo">
              <legend id="accessalls_legend">
                <label class="control-label">{{ labels.accessalls_sectionlabel }}</label>
              </legend>
            </fieldset>							
            <div id="branchflaglayer" class="table-layer-class">
              <div class="row row-height">
                <div class="col-height col-md-8">
                  <div class="checkbox form-check">
                    <input ref="branchflag" type="checkbox" true-value="1" false-value="0" id="branchflag" v-model="localData.branchflag" class="form-control input-md form-check-input" />
                    <label for="branchflag" class="control-label form-check-label">{{ labels.branchflag_label }}</label>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="row row-height">
          <div id="accessbranches" class="col-md-12 col-height">
            <fieldset id="accessbranchesfieldset" :disabled="!isAccessBranch">
              <div id="accessbrancheslayer" class="table-layer-class">
                <div v-for="(branch, index) in branchLists()" :key="index" class="row row-heighter">
                  <div v-for="item in branch" :key="item.id" class="col-height col-md-4">
                    <div class="checkbox form-check form-group">
                      <label class="form-check-label">
                        <input type="checkbox" :value="item.id" v-model="localData.userbranches" class="form-control input-md form-check-input"/>
                        <span class="span-label">{{item.text}}</span>
                      </label>
                    </div>
                  </div>
                </div>
              </div>
            </fieldset>
          </div>
        </div>
        <div class="row row-height">
          <div id="accessroles" class="col-md-12 col-height">
            <fieldset id="accessrolesheaderinfo">
              <legend id="accessrole_legend">
                <label id="accessroles_sectionlabel" class="control-label">{{ labels.accessroles_sectionlabel }}</label>
              </legend>
            </fieldset>									
            <div id="accessroleslayer" class="table-layer-class">				
              <div v-for="(role, index) in roleLists()" :key="index" class="row row-heighter">
                <div v-for="item in role" :key="item.id" class="col-height col-md-4">
                  <div class="checkbox form-check form-group">
                    <label class="form-check-label">
                      <input type="checkbox" :value="item.id" v-model="localData.userroles" class="form-control input-md form-check-input"/>
                      <span class="span-label">{{item.text}}</span>
                    </label>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="row row-height">
          <div id="accessgroups" class="col-md-12 col-height">
            <fieldset id="accessgroupsheaderinfo">
              <legend id="accessgroups_legend">
                <label id="accessgroups_sectionlabel" class="control-label">{{ labels.accessgroups_sectionlabel }}</label>
              </legend>
            </fieldset>									
            <div id="accessgroupslayer" class="table-layer-class">
              <div v-for="(group, index) in groupLists()" :key="index" class="row row-heighter">
                <div v-for="item in group" :key="item.id" class="col-height col-md-4">
                  <div class="checkbox form-check form-group">
                    <label class="form-check-label">
                      <input type="checkbox" :value="item.id" v-model="localData.usergroups" class="form-control input-md form-check-input"/>
                      <span class="span-label">{{item.text}}</span>
                    </label>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
    </template>
    <template v-slot:footer>
      <div class="col-md-3 pull-left" v-if="isRegisteredFactor">
        <button ref="resetfactorbutton" id="resetfactorbutton" class="btn btn-dark btn-sm" @click="resetFactorClick" v-if="showFactor"><em class="fa fa-undo fa-btn-icon"></em>{{ labels.reset_factor_button }}</button>
			</div>
      <button ref="updatebutton" id="updatebutton" class="btn btn-dark btn-sm" @click="updateClick"><em class="fa fa-save fa-btn-icon"></em>{{ labels.update_button }}</button>
      <button class="btn btn-dark btn-sm" data-dismiss="modal"><em class="fa fa-close fa-btn-icon"></em>{{ labels.cancel_button }}</button>
    </template>
  </DialogForm>
</template>
<style>
#branchflag_label { color: navy; }
#resetfactorbutton { min-width: 120px; }
#accessallsheaderinfo { min-height: 35px; padding-left: 5px; }
#accessalls_legend { margin-bottom: 0px; }
#branchflaglayer { padding-left: 5px; }
#accessbrancheslayer { padding-left: 40px; }
#accessrolesheaderinfo { min-height: 35px; padding-left: 5px; }
#accessrole_legend { margin-bottom: 0px; }
#accessroleslayer { padding-left: 5px; }
#accessgroupsheaderinfo { min-height: 35px; padding-left: 5px; }
#accessgroups_legend { margin-bottom: 0px; }
#accessgroupslayer { padding-left: 5px; }
span.span-label { padding-left: 10px; }
</style>
<script>
import { ref, computed, watch } from 'vue';
import { useVuelidate } from '@vuelidate/core';
import { required, helpers } from '@vuelidate/validators';
import $ from "jquery";
import { DEFAULT_CONTENT_TYPE, getApiUrl, disableControls }  from '@willsofts/will-app';
import { startWaiting, stopWaiting, submitFailure, detectErrorResponse }  from '@willsofts/will-app';
import { confirmUpdate, successbox, serializeParameters, alertmsg, confirmDialogBox } from '@willsofts/will-app';
import { InputMask } from '@willsofts/will-control';
import DialogForm from './DialogForm.vue';

const APP_URL = "/api/sfte007";
const defaultData = {
  site: "",
  userid: "",
  username: "",
  usertname: "",
  usertsurname: "",
  userename: "",
  useresurname: "",
  email: "",
  status: "A",
  siteflag: "",
  branchflag: "",
  displayname: "",
  userbranch: "",
  sitedesc: "",
  factorid: "",
  factorflag: "",
  usersites: [],
  userbranches: [],
  userroles: [],
  usergroups: [],
};

export default {
  components: {
    DialogForm, InputMask
  },
  props: {
    modes: Object,
    labels: Object,
    dataCategory: Object
  },
  emits: ["data-updated","data-reseted"],
  setup(props) {
    const localData = ref({ ...defaultData }); 
    const reqalert = ref(props.labels.empty_alert);
    const requiredMessage = () => {
      return helpers.withMessage(reqalert, required);
    }
    const validateRules = computed(() => { 
      return {
        username: { required: requiredMessage() },
      } 
    });
    const showFactor = ref(true);
    const v$ = useVuelidate(validateRules, localData, { $lazy: true, $autoDirty: true });
    return { v$, localData, reqalert, showFactor };
  },
  created() {
    watch(this.$props, (newProps) => {      
      this.reqalert = newProps.labels.empty_alert;
    });
  },
  computed: {
    isRegisteredFactor() {
      return this.localData.factorflag == "1";
    },
    isAccessBranch() {
      return this.localData.branchflag == "1";
    }
  },
  mounted() {
    this.$nextTick(function () {
      $("#modaldialog_layer").find(".modal-dialog").draggable();
    });
  },
  methods: {
    buildLists(category,chunkSize=3) {
      let results = [];
      let tcategory = this.$props.dataCategory[category];
      if(tcategory) {
        for (let i = 0; i < tcategory.length; i += chunkSize) {
          results.push(tcategory.slice(i, i + chunkSize));
        }
      }
      return results;
    },
    branchLists() {
      return this.buildLists("tcompbranch");
    },
    roleLists() {
      return this.buildLists("trole");
    },
    groupLists() {
      return this.buildLists("tgroup");
    },
    reset(newData) {
      if(newData) this.localData = {...newData};
    },
    async updateClick() {
      console.log("click: update",this.isAccessBranch);
      disableControls($("#updatebutton"));
      let valid = await this.validateForm();
      if(!valid) return;
      this.startUpdateRecord();
    },
    async validateForm(focusing=true) {
      const valid = await this.v$.$validate();
      console.log("validate form: valid",valid);
      console.log("error:",this.v$.$errors);
      if(!valid) {
        if(focusing) {
          this.focusFirstError();
        }
        return false;
      }
      return true;
    },
    focusFirstError() {
      if(this.v$.$errors && this.v$.$errors.length > 0) {
        let input = this.v$.$errors[0].$property;
        let el = this.$refs[input];
        if(el) el.focus(); //if using ref
        else $("#"+input).trigger("focus"); //if using id
      }
    },
    showDialog(callback) {
      //$("#modaldialog_layer").modal("show");
      if(callback) $(this.$refs.dialogForm.$el).on("shown.bs.modal",callback);
      $(this.$refs.dialogForm.$el).modal("show");
    },  
    hideDialog() {
      //$("#modaldialog_layer").modal("hide");
      $(this.$refs.dialogForm.$el).modal("hide");
    },
    resetRecord() {
      //reset to default data 
      this.reset(defaultData);
      //reset validator
      this.v$.$reset();
      //enable key field
      this.disabledKeyField = false;
    },
    startUpdateRecord() {
      confirmUpdate(() => {
        if(!this.isAccessBranch) this.localData.userbranches = []; 
        this.updateRecord(this.localData);
      });
    },
    updateRecord(dataRecord) {
        let jsondata = {ajax: true};
        let formdata = serializeParameters(jsondata,dataRecord);
        startWaiting();
        $.ajax({
          url: getApiUrl()+APP_URL+"/update",
          data: formdata.jsondata,
          headers : formdata.headers,
          type: "POST",
          dataType: "json",
          contentType: DEFAULT_CONTENT_TYPE,
          error : function(transport,status,errorThrown) {
            console.error("error: status",status,"errorThrown",errorThrown);
            submitFailure(transport,status,errorThrown);
          },
          success: (data) => {
            stopWaiting();
            console.log("success",data);
            if(detectErrorResponse(data)) return;
            successbox(() => {
              this.hideDialog();
              this.$emit('data-updated',dataRecord,data);
            });
          }
      });
    },
    retrieveRecord(dataKeys) {
      let jsondata = {ajax: true};
      let formdata = serializeParameters(jsondata,dataKeys);
      startWaiting();
      $.ajax({
        url: getApiUrl()+APP_URL+"/retrieve",
        data: formdata.jsondata,
        headers : formdata.headers,
        type: "POST",
        dataType: "json",
        contentType: DEFAULT_CONTENT_TYPE,
        error : function(transport,status,errorThrown) {
          console.error("retrieveRecord: error: status",status,"errorThrown",errorThrown);
          submitFailure(transport,status,errorThrown);
        },
        success: (data) => {
          stopWaiting();
          console.log("retrieveRecord: success",data);
          if(data.body.dataset) {
            this.reset(data.body.dataset);
            this.v$.$reset();
            this.disabledKeyField = true;
            this.showDialog(() => { this.$refs.username.focus(); });
          }
        }
      });	
    },
    resetFactorClick() {
      disableControls($("#resetfactorbutton"));
      this.startResetFactor();
    },
    startResetFactor() {
      this.confirmResetFactor(() => { 
        this.resetFactor({userid: this.localData.userid, factorid: this.localData.factorid});
      });
    },
    confirmResetFactor(okFn, cancelFn,  width, height) {
      if(!confirmDialogBox("QS0021",null,"Do you want to reset two factor authentication?",okFn,cancelFn,width,height)) return false;
      return true;
    },
    resetFactor(dataKeys) {
      let jsondata = {ajax: true};
      let formdata = serializeParameters(jsondata,dataKeys);
      startWaiting();
      $.ajax({
        url: getApiUrl()+APP_URL+"/resetfactor",
        data: formdata.jsondata,
        headers: formdata.headers,
        type: "POST",
        dataType: "json",
        contentType: DEFAULT_CONTENT_TYPE,
        error : function(transport,status,errorThrown) {
          console.error("resetfactor: error: status",status,"errorThrown",errorThrown); 
          submitFailure(transport,status,errorThrown);  
        },
        success: (data) => { 
          stopWaiting();
          console.log("resetFactor: success",data);
          this.showFactor = false;
          alertmsg("QS0202","Reset Two Factor Success",undefined,() => {
            this.$emit('data-reseted',dataKeys,data);
          });
        }
      });	
    },
  }
};
</script>
