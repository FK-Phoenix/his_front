<template>
  <div id="constant">
    <el-row :gutter="20">
      <el-col :span="4">
        <el-input type="text" v-model="constid" placeholder="ID" clearable></el-input>
      </el-col>
      <el-col :span="4">
        <el-input type="text" v-model="consttype" placeholder="Type" clearable></el-input>
      </el-col>
      <el-col :span="4">
        <el-input type="text" v-model="constname" placeholder="Name" clearable></el-input>
      </el-col>
      <el-col :span="4">
        <el-input type="text" v-model="constcode" placeholder="Code" clearable></el-input>
      </el-col>
      <el-col :span="4">
        <el-button icon="el-icon-search" type="primary" @click="search">Search</el-button>
      </el-col>
      <el-col :span="4">
        <el-button icon="el-icon-circle-plus-outline" type="primary" @click="showDialogForm('Add')">Add
        </el-button>
      </el-col>
    </el-row>
    <template>
      <el-table :data="consts.slice((currentPage-1)*pagesize,currentPage*pagesize)" style="width: 100%">
        <el-table-column prop="constid" label="ID" width="180">
        </el-table-column>
        <el-table-column prop="consttype" label="Type" width="180">
        </el-table-column>
        <el-table-column prop="constname" label="Name" width="180">
        </el-table-column>
        <el-table-column prop="constcode" label="Code">
        </el-table-column>
        <el-table-column fixed="right" label="Operations" width="120">
          <template slot-scope="scope">
            <el-button @click="showDeleteDialogForm(scope.$index, scope.row)" type="text" size="small">
              delete
            </el-button>
            <el-button @click="showDialogForm('Update',scope.$index, scope.row)" type="text" size="small">
              update</el-button>
          </template>
        </el-table-column>
      </el-table>

      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage" :page-sizes="[5, 10, 20, 40]" :page-size="pagesize" :hide-on-single-page="value" layout="total, sizes, prev, pager, next, jumper" :total="consts.length">
      </el-pagination>

    </template>


    <el-dialog :title="formTitle" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <!--<el-form-item v-if="methodType=='Add'" label="ID" :label-width="formLabelWidth">
            <el-input v-model="form.constid" autocomplete="off"></el-input>
        </el-form-item>-->
        <el-form-item label="Type" :label-width="formLabelWidth">
          <el-input v-model="form.consttype" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Name" :label-width="formLabelWidth">
          <el-input v-model="form.constname" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Code" :label-width="formLabelWidth">
          <el-input v-model="form.constcode" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">Cancel</el-button>
        <el-button type="primary" @click="onSubmit">{{methodType}}</el-button>
      </div>
    </el-dialog>

    <el-dialog title="Confirm Delete" :visible.sync="deleteDialogVisible" width="30%">
      <span>Are you sure you want to delete this?</span>
      <span slot="footer" class="dialog-footer">
                <el-button @click="deleteDialogVisible = false">Cancel</el-button>
                <el-button type="primary" @click="onSubmitDelete">Confirm</el-button>
            </span>
    </el-dialog>
  </div>
</template>

<script>
    export default {
      name: "constant",
      data(){
        return {
          constid: "",
          consttype: "",
          constname: "",
          constcode: "",
          consts: [],

          value: false,
          currentPage: 1, //初始页
          pagesize: 10, //    每页的数据


          methodType: "", //调用方法，add or update
          formTitle: "",
          dialogFormVisible: false,
          updatingIndex: -1,
          updatingObject: {},

          //delete
          deleteDialogVisible: false, //控制弹出删除窗口
          deletingIndex: -1,
          deletingObject: {},

          form: {
            constid: "",
            consttype: "",
            constname: "",
            constcode: "",
          },
          formLabelWidth: '120px'
        }
      },

      mounted: function(){
        console.log("refresh!!!");
        this.currentPage = 1;
        var that = this;
        this.$axios.post('/api/management/consts', {})
          .then(function (response) {
            console.log(response.data);
            that.consts = response.data;
          })
          .catch(function (error) {
            console.log(error);
          });
      },

      methods: {
        search: function () {
          this.currentPage = 1;
          var that = this;
          this.$axios.post('/api/management/consts', {
            constid: that.constid,
            consttype: that.consttype,
            constname: that.constname,
            constcode: that.constcode,
          })
            .then(function (response) {
              console.log(response.data);
              that.consts = response.data;
            })
            .catch(function (error) {
              console.log(error);
            });
        },

        onSubmit: function () {
          if (this.methodType == "Add")
            this.onSubmitAdd();
          if (this.methodType == "Update")
            this.onSubmitUpdate();
        },

        onSubmitAdd: function () {
          var that = this;
          console.log(that.form);
          this.$axios.post('/api/management/addConst', {
            constid: that.form.constid,
            consttype: that.form.consttype,
            constname: that.form.constname,
            constcode: that.form.constcode
          })
            .then(function (response) {
              console.log(response.data);
              that.dialogFormVisible = false;
              that.refresh();
              // that.consts=response.data;
            })
            .catch(function (error) {
              console.log(error);
            });
        },

        onSubmitUpdate: function () {
          var that = this;
          console.log(that.form);
          this.$axios.post('/api/management/updateConst', {
            constid: that.form.constid,
            consttype: that.form.consttype,
            constname: that.form.constname,
            constcode: that.form.constcode
          }).then(function (response) {
            console.log(response.data);
            that.dialogFormVisible = false;
            that.refresh();
            // that.consts=response.data;
          }).catch(function (error) {
            console.log(error);
          });
        },

        //---------DELETE---------/
        showDeleteDialogForm: function (index, row) {
          this.deleteDialogVisible = true;
          this.deletingIndex = index;
          this.deletingObject = row;
        },

        onSubmitDelete: function () {
          console.log(this.users);
          console.log(this.deletingObject);

          var that = this;

          this.$axios.post('/api/management/delConst', {
            constid: that.deletingObject.constid + ""
          }).then(function (response) {
            console.log(response.data);
            that.refresh();
            // that.consts=response.data;
          }).catch(function (error) {
            console.log(error);
          });
          this.deleteDialogVisible = false;
        },

        showDialogForm: function (method, index, row) {
          if (method == "Add") {
            this.form = {
              constid: "",
              consttype: "",
              constname: "",
              constcode: ""
            }
          }
          if (method == "Update")
            this.form = row;
          console.log(this.form);

          this.dialogFormVisible = true;
          this.methodType = method;
          this.formTitle = method + " Constant";
          this.updatingIndex = index;
          this.updatingObject = row;
          console.log("Method using " + this.methodType);
          console.log("index " + index);
          console.log("row ");
          console.log(row)
        },

        refresh: function () {
          this.currentPage = 1;
          var that = this;
          this.$axios.post('api/management/consts', {
            constid: "",
            consttype: "",
            constname: "",
            constcode: "",
          })
            .then(function (response) {
              console.log(response.data);
              that.consts = response.data;
            })
            .catch(function (error) {
              console.log(error);
            });
        },

        handleSizeChange: function (pagesize) {
          this.pagesize = pagesize;
          console.log(pagesize);
        },
        handleCurrentChange: function (currentPage) {
          this.currentPage = currentPage;
          console.log(currentPage);
        },
      }
    }
</script>

<style scoped>

</style>
