<template>
  <div id="medtech">

    <el-row :gutter="20">
      <el-col :span="4">
        <el-input type="text" v-model="itemcode" placeholder="Code"></el-input>
      </el-col>
      <el-col :span="4">
        <el-input type="text" v-model="feecode" placeholder="Feecode"></el-input>
      </el-col>
      <el-col :span="4">
        <el-input type="text" v-model="itemname" placeholder="Name"></el-input>
      </el-col>
      <el-col :span="4">
        <el-input type="text" v-model="deptcode" placeholder="Department"></el-input>
      </el-col>

      <el-col :span="4">
        <el-button icon="el-icon-search" type="primary" @click="search">Search</el-button>
      </el-col>
      <el-col :span="4">
        <el-button icon="el-icon-circle-plus-outline" type="primary" @click="showDialogForm('Add')">Add New Entry
        </el-button>
      </el-col>
    </el-row>


    <template>
      <el-table :data="nonmedics.slice((currentPage-1)*pagesize,currentPage*pagesize)" style="width: 100%">
        <el-table-column prop="itemcode" label="Code" width="380">
        </el-table-column>
        <el-table-column prop="feecode" label="Feecode" width="100">
        </el-table-column>
        <el-table-column prop="deptcode" label="Department Code" width="180">
        </el-table-column>
        <el-table-column prop="itemname" label="Name" width="440">
        </el-table-column>
        <el-table-column prop="size" label="Size" width="180">
        </el-table-column>
        <el-table-column prop="price" label="Price">
        </el-table-column>

        <el-table-column fixed="right" label="Operations" width="120">
          <template slot-scope="scope">
            <el-button @click="showDeleteDialogForm(scope.$index, scope.row)" type="text" size="small">
              delete
            </el-button>
            <el-button @click="showDialogForm('Update',scope.$index, scope.row)" type="text" size="small">
              update
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage"
                     :page-sizes="[5, 10, 20, 40]" :page-size="pagesize" :hide-on-single-page="value"
                     layout="total, sizes, prev, pager, next, jumper" :total="nonmedics.length">
      </el-pagination>
    </template>


    <el-dialog :title="formTitle" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item v-if="methodType=='Add'" label="Code" :label-width="formLabelWidth">
          <el-input v-model="form.itemcode" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Feecode" :label-width="formLabelWidth">
          <el-input v-model="form.feecode" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Department Code" :label-width="formLabelWidth">
          <el-input v-model="form.deptcode" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Name" :label-width="formLabelWidth">
          <el-input v-model="form.itemname" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Size" :label-width="formLabelWidth">
          <el-input v-model="form.size" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Price" :label-width="formLabelWidth">
          <el-input v-model="form.price" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">Cancel</el-button>
        <el-button type="primary" @click="onSubmit">{{methodType}}</el-button>
      </div>
    </el-dialog>

    <!--    Delete Dialogue -->
    <el-dialog title="Confirm Delete" :visible.sync="deleteDialogVisible" width="30%">
      <span>Are you sure you want to delete this?</span>
      <span slot="footer" class="dialog-footer">
                <el-button @click="deleteDialogVisible = false">Cancel</el-button>
                <el-button type="primary" @click="delNonMedicItem">Confirm</el-button>
            </span>
    </el-dialog>

  </div>
</template>

<script>
    export default {
      name: "medtech",
      data() {
        return{
          itemcode: "",
          feecode: "",
          deptcode: "",
          itemname: "",
          size: "",
          price: "",
          nonmedics: [],
          value:false,

          currentPage: 1, //初始页
          pagesize: 10, //    每页的数据

          deleteDialogVisible: false, //控制弹出删除窗口
          deletingIndex: -1,
          deletingObject: {},

          methodType: "", //add or update
          formTitle: "",
          dialogFormVisible: false,
          updatingIndex: -1,
          updatingObject: {},
          form: {
            itemcode: "",
            feecode: "",
            deptcode: "",
            itemname: "",
            size: "",
            price: "",
          },
          formLabelWidth: '120px'
        }
      },

      mounted: function(){
        console.log("refresh!!!")
        this.currentPage = 1;
        var that = this;
        this.$axios.post('/api/management/nonmedics', {})
          .then(function (response) {
            console.log(response.data);
            that.nonmedics = response.data;
          })
          .catch(function (error) {
            console.log(error);
          });
      },

      methods: {
        search: function() {
          this.currentPage = 1;
          var that = this;
          this.$axios.post('/api/management/nonmedics', {
            itemcode: that.itemcode,
            feecode: that.feecode,
            deptcode: that.deptcode,
            itemname: that.itemname,
            size: that.size,
            price: that.price,
          })
            .then(function(response) {
              console.log(that.form.itemcode);
              console.log(response.data);
              that.nonmedics = response.data;
            })
            .catch(function(error) {
              console.log(error);
            });
        },

        refresh: function() {
          this.currentPage = 1;
          var that = this;
          this.$axios.post('/api/management/nonmedics', {
            itemcode: "",
            feecode: "",
            deptcode: "",
            itemname: "",
            size: "",
            price: "",
          })
            .then(function(response) {
              console.log(that.form.itemcode);
              console.log(response.data);
              that.nonmedics = response.data;
            })
            .catch(function(error) {
              console.log(error);
            });
        },

        onSubmit: function() {
          if (this.methodType === "Add")
            this.onSubmitAdd();
          if (this.methodType === "Update")
            this.onSubmitUpdate();
          this.dialogFormVisible = false;
          this.refresh();
        },

        onSubmitAdd: function() {
          var that = this;
          console.log(that.form);
          this.$axios.post('/api/management/addNonMedic', {
            itemcode: that.form.itemcode,
            feecode: that.form.feecode,
            deptcode: that.form.deptcode,
            itemname: that.form.itemname,
            size: that.form.size,
            price: that.form.price,
          })
            .then(function(response) {
              console.log(response.data);
              // that.depts=response.data;
            })
            .catch(function(error) {
              console.log(error);
            });
        },

        onSubmitUpdate: function() {
          var that = this;
          console.log(that.form);
          this.$axios.post('/api/management/updateNonMedic', {
            itemcode: that.form.itemcode,
            feecode: that.form.feecode,
            deptcode: that.form.deptcode,
            itemname: that.form.itemname,
            size: that.form.size,
            price: that.form.price,
          })
            .catch(function(error) {
              console.log(error);
            });
        },

        showDeleteDialogForm: function(index, row) {
          this.deleteDialogVisible = true;
          this.deletingIndex = index;
          this.deletingObject = row;
        },

        delNonMedicItem: function() {
          var params = new URLSearchParams();
          params.append('itemcode', this.deletingObject.itemcode);
          this.$axios.post('/api/management/delNonMedic', params)
            .then(function(response) {
              // console.log(index.data);
              /*console.log(that.deptcode);
              console.log(response.deptcode);
              that.deptcode = row.deptcode;
              that.deptcode = response.deptcode;*/
            })
            .catch(function(error) {
              console.log(error);
            });
          this.deleteDialogVisible = false;
          this.refresh();

        },

        showDialogForm: function(method, index, row) {
          if (method === "Add") {
            this.form = {
              itemcode: "",
              feecode: "",
              deptcode: "",
              itemname: "",
              size: "",
              price: "",
            }
          }
          if (method === "Update") {
            this.form = {
              itemcode: row.itemcode,
              feecode: row.feecode,
              deptcode: row.deptcode,
              itemname: row.itemname,
              size: row.size,
              price: row.price,
            }
          }

          this.dialogFormVisible = true;
          this.methodType = method;
          this.formTitle = method + " Non-Medical Item";
          this.updatingIndex = index;
          this.updatingObject = row;
        },


        handleSizeChange: function(pagesize) {
          this.pagesize = pagesize;
          console.log(pagesize);
        },
        handleCurrentChange: function(currentPage) {
          this.currentPage = currentPage;
          console.log(currentPage);
        }
      }
    }
</script>

<style scoped>

</style>
