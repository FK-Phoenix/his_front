<template>
  <div id="register">

    <el-row>
      <el-card class="box-card">
        <div style="font-weight: bold; font-size: larger">Registration</div>
      </el-card>
    </el-row>
    <el-row :gutter="10" style="margin-top: 10px;">
      <el-col :span="9">
        <el-card class="box-card" style="height: 800px">
          <div slot="header" class="clearfix">
            <span style="font-weight: bold">Patient Information</span>
            <el-button style="float: right; padding: 3px 0" type="text" @click="clearPInfo">Clear</el-button>
          </div>
          <div style="width: 90%; margin-left: 15px;">
            <div style="margin-top: 10px;" class="hintFont">ID：</div>
            <el-input placeholder="Patient ID" v-model="patientInfo.pid" size="medium" v-on:blur="tryCompletePInfo()"></el-input>
            <div class="hintFont">Name:</div>
            <el-input placeholder="Name" v-model="patientInfo.pname" size="medium"></el-input>
            <div class="hintFont">Age:</div>
            <el-input placeholder="Age" v-model="patientInfo.page" size="medium"></el-input>
            <el-row type="flex" justify="space-between" align="center">
              <div class="hintFont">Birth Date(optional):</div>
              <el-date-picker
                style="margin-top: 12px;"
                v-model="patientInfo.pbirth" type="date"
                placeholder="Birth Date"
                size="medium">
              </el-date-picker>
            </el-row>
            <div class="hintFont">Address：</div>
            <el-input placeholder="Address" v-model="patientInfo.paddress" size="medium"></el-input>
            <el-row type="flex" justify="space-between" align="center">
              <div class="hintFont">Gender：</div>
              <el-row >
                <el-radio-group style="margin-top: 25px;" v-model="patientInfo.pgender">
                  <el-radio :label="true">Male</el-radio>
                  <el-radio :label="false">Female</el-radio>
                </el-radio-group>
              </el-row>
            </el-row>

            <!-- Billing Category -->
            <el-row type="flex" justify="space-between" align="center">
              <div class="hintFont">Billing Category：</div>
              <el-select  style="margin-top: 12px;" v-model="billcat" placeholder="Billing category"
                          size="medium">
                <el-option v-for="item in bills" :key="item.constid" :label="item.constname" :value="item.constname">
                </el-option>
              </el-select>
            </el-row>

            <el-row class="hintFont">
              <el-checkbox v-model="newrecord">New Medical Record</el-checkbox>
            </el-row>
          </div>

        </el-card>
      </el-col>

      <el-col :span="15">
        <el-card class="box-card" style="height: 800px">
          <div slot="header" class="clearfix">
            <span style="font-weight: bold">Shifts Choosing</span>
            <el-button style="float: right; padding: 3px 0" type="text" v-on:click="showPaymentDialogForm">Clear</el-button>
          </div>
          <el-row :gutter="20" type="flex" align="middle">
            <!-- Am or PM -->
            <el-col :span="8" type="flex" align="middle">
              <el-radio-group @change="onChangeScreeningConditions()" v-model="screeningConditions.aorp">
                <el-radio :label="true">Morning</el-radio>
                <el-radio :label="false">Afternoon</el-radio>
              </el-radio-group>
            </el-col>

            <!-- Departments -->
            <el-col :span="6" style="margin-left: 20px;">
              <el-select  v-model="screeningConditions.deptcode" placeholder="Department"
                          @change="onChangeScreeningConditions()" size="medium" >
                <el-option v-for="item in depts" :key="item.deptcode" :label="item.deptname" :value="item.deptcode">
                </el-option>
              </el-select>
            </el-col>

            <!-- registrationLevel -->
            <el-col :span="6">
              <el-select v-model="screeningConditions.registrationLevel" placeholder="Register level"
                         @change="onChangeScreeningConditions()" size="medium">
                <el-option v-for="item in registrationLevels" :key="item.itemcode" :label="item.itemname" :value="item.itemname">
                </el-option>
              </el-select>
            </el-col>
          </el-row>

          <el-divider></el-divider>

          <el-row :gutter="10" style="margin-top:20px;">
            <el-col :span="14">
              <el-card shadow="never" style="height: 90%">
                <el-table
                  ref="singleTable"
                  v-bind:data="shifts"
                  highlight-current-row
                  style="height: 480px">
                  <el-table-column type="index" width="50">
                  </el-table-column>
                  <el-table-column property="realname" label="Doctor Name" width="120">
                  </el-table-column>
                  <el-table-column property="balance" label="Spots Left" width="120">
                  </el-table-column>
                  <el-table-column style="text-align: center" label="" width="40">
                    <template slot-scope="scope">
                      <i size="medium" class="el-icon-circle-plus-outline"
                         v-on:click="showPaymentDialogForm(scope.row)"></i>
                    </template>
                  </el-table-column>
                </el-table>
              </el-card>
            </el-col>
            <el-col :span="9" style="padding-left: 10px;">
              <el-collapse accordion v-model="activeName">
                <el-collapse-item name="1">
                  <template slot="title">
                    Payment & Confirmation
                  </template>
                  <el-row type="flex" justify="space-between">
                    <el-col :span="15">
                      Registration Fee
                    </el-col>
                    <el-col :span="4">
                      {{fee}}元
                    </el-col>
                  </el-row>
                  <el-row v-if="newrecord" type="flex" justify="space-between">
                    <el-col :span="15">
                      Medical Record Fee
                    </el-col>
                    <el-col :span="4">
                      1元
                    </el-col>
                  </el-row>
                  <el-divider></el-divider>
                  <el-row type="flex" justify="space-between">
                    <el-col :span="15">
                      Total Price
                    </el-col>
                    <el-col v-if="newrecord" :span="4">
                      {{fee+1}}元
                    </el-col>
                    <el-col v-else :span="4">
                      {{fee}}元
                    </el-col>
                  </el-row>
                  <el-row style="margin-top: 30px;" type="flex" justify="end">
                    <el-button round size="medium" @click="onConfirm">Confirm</el-button>
                  </el-row>
                </el-collapse-item>
              </el-collapse>
            </el-col>
          </el-row>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
    export default {
      name: "register",
      data(){
          return {
            patientInfo: {
              pid: "",
              pname: "",
              page: "",
              pbirth: "",
              paddress: "",
              pgender: true,
            },
            patientExists: false,

            //To get all the shifts available we need:
            screeningConditions:{
              deptcode:"",
              aorp: true,
              registrationLevel:""
            },

            billcat: "",
            newrecord: false,
            fee: "",
            selectedShift:{},

            paymentFormVisible: false,
            activeName: '',

            bills: [], // Drop Down for billing Categories
            depts: [], //Drop down for departments
            registrationLevels:[], //Drop down for registrationLevels
            //shifts in the table
            shifts:[],

            insertedBill: [],
            receiptInfo:{
              recid:""
            }
          }
      },
      mounted: function(){
        console.log("refreshed");
        var that = this;

        this.$axios({
          url: '/api/registration/getDoctorsAvailable',
          method: 'post',
          contentType: 'application/json', // 这句不加出现415错误:Unsupported Media Type
          data:  this.screeningConditions,
        })
          .then(function (response) {
            console.log("success getting shifts...");
            console.log(response.data);
            that.shifts = response.data;
          })
          .catch(function (error) {
            console.log(error);
          });


        //Billing Category
        this.$axios.post('/api/management/consts', {
          constid: "",
          consttype: "billcat",
          constname: "",
          constcode: ""
        })
          .then(function (response) {
            // console.log(response.data);
            that.bills = response.data;
            // console.log("billing category");
            // console.log(that.bills);
          })
          .catch(function (error) {
            console.log(error);
          });

        this.$axios.post('/api/management/depts', {})
          .then(function (response) {
            // console.log(response.data);
            that.depts = response.data;
          })
          .catch(function (error) {
            console.log(error);
          });

        this.$axios.post('/api/management/nonmedics', {
          itemcode: "",
          feecode: "GHF",
          deptcode: "",
          itemname: "",
          size: "",
          price: "",
        })
          .then(function (response) {
            // console.log(response.data);
            that.registrationLevels = response.data;
          })
          .catch(function (error) {
            console.log(error);
          });
      },

      methods: {
        refresh:function(){
          console.log("Refresh!!!");
          var that = this;
          this.$axios({
            url: '/api/registration/getDoctorsAvailable',
            method: 'post',
            contentType: 'application/json',
            data:  this.screeningConditions,
          })
            .then(function (response) {
              console.log("successful got shifts");
              that.shifts=[];
              that.shifts = response.data;
              console.log("refresh->that.shifts");
              console.log(that.shifts);
            })
            .catch(function (error) {
              console.log(error);
            });
        },

        clearPInfo(){
          this.patientInfo = {};
          this.billcat = ""
        },

        onChangeScreeningConditions(){
          console.log("onChangescreeningConditions");
          console.log(this.screeningConditions);
          var that = this;
          this.$axios({
            url: '/api/registration/getDoctorsAvailable',
            method: 'post',
            contentType: 'application/json',
            data:  this.screeningConditions,
          })
            .then(function (response) {
              console.log("successful got shifts");
              that.shifts = response.data;
            })
            .catch(function (error) {
              console.log(error);
            });
        },

        // -----------PAYMENT STUFF --------------/
        calculatePayment: function() {
          var that = this;
          this.$axios({
            url: '/api/registration/getRegistrationPrice',
            method: 'post',
            contentType: 'application/json', // 这句不加出现415错误:Unsupported Media Type
            data:{
              selectedShift: that.selectedShift,
            },
          })
            .then(function (response) {
              console.log("success get price");
              that.fee = response.data;
            })
            .catch(function (error) {
              console.log(error);
            });
        },

        showPaymentDialogForm: function(row){
          this.$message({
            message: 'Picked',
          });
          this.selectedShift = row;
          console.log("selectedShift");
          console.log(this.selectedShift);
          console.log("showPaymentDialogForm");
          this.activeName = '1';
          this.calculatePayment();
          console.log("this.selectedShift = row;");
          console.log(this.selectedShift);
        },

        onConfirm: function() {
          console.log("onConfirm");
          console.log(this.patientInfo);
          var that = this;
          this.$axios({
            url: '/api/registration/submitRegistration',
            method: 'post',
            contentType: 'application/json', // 这句不加出现415错误:Unsupported Media Type
            data:{
              patient: that.patientInfo,
              patientExists: that.patientExists,
              selectedShift: that.selectedShift,
              billcat: that.billcat,
              newrecord: that.newrecord
            },
          })
            .then(function (response) {
              console.log("success register");
              console.log("Inserted Bill")
              console.log(response.data);
              that.insertedBill = response.data;
              that.$notify({
                title: 'Success',
                message: 'Successfully registered.',
                type: 'success'
              });
              that.logReceipt();
              that.activeName = "";
              that.clearPInfo();
              that.refresh();
            })
            .catch(function (error) {
              console.log(error);
            });
        },

        tryCompletePInfo:function () {
          console.log("Blur");
          var that = this;
          that.patientExists = false;
          this.$axios({
            url: '/api/registration/tryCompletePatientInfo',
            method: 'post',
            contentType: 'application/json', // 这句不加出现415错误:Unsupported Media Type
            data:{id: that.patientInfo.pid},
          })
            .then(function (response) {
              console.log("success complete pinfo");
              console.log(response.data);
              if(response.data.exists==="Yes"){
                that.patientInfo = response.data.patient;
                that.patientExists = true;
                that.$message({
                  message: 'Successfully retrieved patient info',
                  type: 'success'
                });
                if(!response.data.lastRegFinished){
                  that.$confirm('The patient is still being treated. No registration available.', 'Attention', {
                    confirmButtonText: 'Confirm',
                    type: 'warning',
                    customClass: 'messageBox-prompt'
                  });
                  that.clearPInfo();
                }
              }
              else{
                that.$message('Did not find patient with the ID');
              }
            })
            .catch(function (error) {
              console.log(error);
            });
        },

//------------------Receipt--------------------------------
        logReceipt(){
          var that = this;
          this.$axios({
            url: '/api/charge/logReceipt',
            method: 'post',
            contentType: 'application/json', // 这句不加出现415错误:Unsupported Media Type
            data:{
              patientInfo: that.patientInfo,
              bills: that.insertedBill,
              confirmType: "Charge",
              //TODO: Add chargerID 管理员here
              chargerid: 1
            }
          })
            .then(function (response) {
              console.log("success logReceipt");
              that.receiptInfo.recid = response.data.recid;
              console.log(that.receiptInfo.recid);
              that.$router.push({ path: '/receipt_preview/'+ that.receiptInfo.recid });
              // window.open("receipt-preview.html?"+"recid="+that.receiptInfo.recid);
            })
            .catch(function (error) {
              console.log(error);
            });
        },
      }
    }
</script>

<style>
  body{
    font-family: Helvetica, sans-serif;
  }
  .hintFont{
    margin-top: 20px;
    margin-bottom: 10px;
    float: left;
  }
</style>
