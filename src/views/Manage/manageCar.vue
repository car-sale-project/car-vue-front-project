<template>
  <div class="managecar">
    <h1>汽车管理<i class="fa fa-car" aria-hidden="true"></i></h1>

    <el-divider></el-divider>
    <!-- 添加汽车 -->
    <div id="round" style="float:right">
      <el-menu>
        <el-menu-item>
          <el-col :span="4">
            <el-button type="primary" @click="showAdddaillog">添加汽车</el-button >
          </el-col>
        </el-menu-item>
      </el-menu>
    </div>
    <!-- 返回管理中心 -->
    <el-page-header @back="goBack" content></el-page-header>

    <el-table
      :data="tableData"
      :header-cell-style="tableHeaderColor"
      :cell-style="tableCellStyle"
      style="width: 100%"
      max-height="500"
    >
      <!-- header-cell-style="{background:'#FAFA03'}" -->
      <!-- 汽车基本信息 -->
      <el-table-column prop="carId" label="汽车编号" width="100">
        <template slot-scope="scope">
          {{ scope.row.carid }}
        </template>
      </el-table-column>

      <el-table-column prop="carName" label="汽车名称" width="160">
        <template slot-scope="scope">
          {{ scope.row.carname }}
        </template>
      </el-table-column>

      <el-table-column prop="carBrand" label="汽车品牌" width="160">
        <template slot-scope="scope">
          {{ scope.row.carbrand }}
        </template>
      </el-table-column>

      <el-table-column prop="carModel" label="汽车型号" width="160">
        <template slot-scope="scope">
          {{ scope.row.carmodel }}
        </template>
      </el-table-column>

      <el-table-column prop="carPrice" label="汽车价格" width="170">
        <template slot-scope="scope">
          {{ scope.row.carprice }}
        </template>
      </el-table-column>

      <!-- <el-table-column prop='carStock' label='汽车库存' width="160">
      <template slot-scope="scope">
        {{scope.row.carstock}}
      </template>
    </el-table-column> -->

      <el-table-column prop="carNote" label="汽车简介" width="280">
        <template slot-scope="scope">
          {{ scope.row.carnote }}
        </template>
      </el-table-column>

      <!-- 库存增减 -->
      <el-table-column align="center" label=" " width="60">
        <template slot-scope="scope">
          <span
            style="color:#35EB15;font-size:16px"
            @click.prevent="addRow(scope.row, scope.$index)"
            ><i class="el-icon-circle-plus"></i
          ></span>
        </template>
      </el-table-column>

      <el-table-column prop="stock" label="库存" align="center" width="110">
        <template slot-scope="scope">
          <el-input
            v-model="scope.row.carstock"
            oninput="value=value.replace(/[^\d]/g,'')"
            maxlength="3"
          >
            {{ scope.row.carstock }}
          </el-input>
        </template>
      </el-table-column>

      <el-table-column align="center" label=" " width="60">
        <template slot-scope="scope">
          <span
            style="color:#FFB7E9;font-size:16px"
            @click.prevent="delRow(scope.row, scope.$index)"
            ><i class="el-icon-remove"></i
          ></span>
        </template>
      </el-table-column>

      <!-- ///////////////////////////// -->
      <!-- <el-table-column label="库存" align="center" width="200">
                <template>
                  <el-input-number v-model="num" @change="handleChange" :min="1" :max="10" label="库存"></el-input-number>
                </template>
    </el-table-column>   -->

      <!-- <el-table-column label="库存" width="280" header-align="center" align="center">
        <template slot-scope="scope">
          <el-input-number
            v-model="scope.row.quantity"
            @change="handleChange($event,scope.row.carName)"
            :min="1"
            :max="10"
            label="库存"
          ></el-input-number>
        </template>
      </el-table-column> -->

      <el-table-column fixed="right" label="编辑" width="180" align="center">
        <template slot-scope="scope">
          <el-button
            type="primary"
            icon="el-icon-edit"
            circle
            size="mini"
            @click="ShowEditCar(scope.row)"
          ></el-button>
          <el-button
            @click.native.prevent="
              deleteRow(scope.$index, scope.row, tableData)
            "
            type="danger"
            icon="el-icon-delete"
            plain
            size="mini"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <div class="block">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="this.page"
        :page-sizes="[5, 10, 20, 40]"
        :page-size="this.offset"
        layout="total, sizes, prev, pager, next, jumper"
        :total="this.total"
      >
      </el-pagination>
    </div>

    <!-- 编辑汽车信息 -->
    <el-dialog
      title="编辑汽车信息"
      :visible.sync="editdailgVisible"
      width="50%"
      @click="editdaillogClose"
    >
      <el-form ref="editFormRef" v-model="this.car" label-width="80px">
        <el-form-item label="汽车编号" prop="carId">
          <el-input v-model="car.carid"></el-input>
        </el-form-item>
        <el-form-item label="汽车名称" prop="carName">
          <el-input v-model="car.carname"></el-input>
        </el-form-item>
        <el-form-item label="汽车品牌" prop="carBrand">
          <el-input v-model="car.carbrand"></el-input>
        </el-form-item>
        <el-form-item label="汽车型号" prop="carModel">
          <el-input v-model="car.carmodel"></el-input>
        </el-form-item>
        <el-form-item label="图片链接" prop="carImg">
          <el-input v-model="car.carimg"></el-input>
        </el-form-item>
        <el-form-item label="汽车价格" prop="carPrice">
          <el-input v-model="car.carprice"></el-input>
        </el-form-item>
        <el-form-item label="库存" prop="carStock">
          <el-input v-model="car.carstock"></el-input>
        </el-form-item>
        <el-form-item label="汽车简介" prop="carNote">
          <el-input v-model="car.carnote"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editdailgVisible = false">取 消</el-button>
        <el-button type="primary" @click="editcar">保 存</el-button>
      </span>
    </el-dialog>

    <!-- 添加汽车 -->
    <el-dialog
      title="添加汽车"
      :visible.sync="adddialogVisible"
      width="50%"
      @click="adddialogClose"
    >
      <el-form ref="addFormRef" v-model="this.carForm" label-width="80px">
        <el-form-item label="汽车名" prop="carName">
          <el-input v-model="carForm.carname"></el-input>
        </el-form-item>
        <el-form-item label="品牌" prop="carBrand">
          <el-input v-model="carForm.carbrand"></el-input>
        </el-form-item>
        <el-form-item label="库存" prop="carStock ">
          <el-input v-model="carForm.carstock"></el-input>
        </el-form-item>
        <el-form-item label="价格" prop="carPrice">
          <el-input v-model="carForm.carprice"></el-input>
        </el-form-item>
        <el-form-item label="型号" prop="carModel">
          <el-input v-model="carForm.carmodel"></el-input>
        </el-form-item>
        <el-form-item label="简介" prop="carNote">
          <el-input v-model="carForm.carnote"></el-input>
        </el-form-item>
        <el-form-item label="图片" prop="carImg">
          <input type="file" id="id" name="image" class="getImgUrl_file" @change="shangc($event)" accept="image/jpg,image/jpeg,imagepng"/>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="adddialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addcar">添 加</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "managecar",
  mounted() {
    const _this = this;
    this.$axios
      .get(
        "admin/get_all_car_by_page?page=" + this.page + "&offset=" + this.offset
      )
      .then((res) => {
        this.tableData = res.data.list;
        this.total = res.data.total;
        // this.car = res.data.list;
        // console.log(res.data);
      });
  },
  methods: {
    //返回管理中心
    goBack() {
      this.$router.go(-1);
    },

    //base64
            shangc(e){
            let files = document.getElementById('id').files[0];
            // console.log(files);
            // let name = document.getElementById('id').files[0].name;
            // let arr = name.split('.');
            // console.log(arr);
            let fileSize = 0;
            let fileMaxSize = 10240;//1M
            if(files){
                fileSize =files.size;          
                if (fileSize > 10*1024*1024) {
                    alert("文件大小不能大于10M！");
                    file.value = "";
                    return false;
                }else if (fileSize <= 0) {
                    alert("文件大小不能为0M！");
                    file.value = "";
                    return false;
                }
            }else{
                return false;
            }

	   //转码base64
            let reader = new FileReader();
            let imgFile
            // let that = this
            reader.readAsDataURL(files)
            reader.onload = e => {
                imgFile = e.target.result;
                let arr = imgFile.split(',')
               this.carForm.carimg = arr.join();
              //  console.log(this.carForm.carimg);
            }
            
        },  

   
    //分页
    handleSizeChange(val) {
      this.offset = val;
      this.getData(this.page, this.offset);
    },
    handleCurrentChange(val) {
      this.page = val;
      this.getData(this.page, this.offset);
    },
    getData(page, offset) {
      this.$axios
        .get(
          "admin/get_all_car_by_page?page=" +
            this.page +
            "&offset=" +
            this.offset
        )
        .then((res) => {
          // console.log(res.data);
          this.tableData = res.data.list;
          // this.car = res.data.list;
          this.total = res.data.total;
        });
    },
    //删除行操作
    deleteRow(index, row, rows) {
      rows.splice(index, 1);
      this.$axios.post("admin/delete_by_id?carid=" + row.carid).then((res) => {
        if (res.data.status === 0) {
          this.$message({
            message: "删除成功！",
            type: "success",
          });
        }
      });
    },
    showAdddaillog() {
      this.adddialogVisible = true;
    },
    adddialogClose() {
      this.$refs.addFormRef.resetFields();
    },
    ShowEditCar(car) {
      this.car = car;
      this.editdailgVisible = true;
    },

    //修改表格表头背景色
    tableHeaderColor({ row, column, rowIndex, columnIndex }) {
      if (rowIndex === 0) {
        return "background-color: #a1afc9;color: #fff;font-weight: 500;";
      }
    },
    // 修改表格行的背景色
    tableCellStyle({ row, column, rowIndex, columnIndex }) {
      if ((columnIndex === 0, 1, 2, 3)) {
        return "background-color: #f3f9f1";
        return "cell-grey";
      }
    },

    //添加汽车
    addcar() {
      this.$axios
        .post("admin/insert_car", this.$qs.stringify(this.carForm))
        .then((res) => {
          if (res.data.status === 0) {
            this.$message({
              message: "添加成功！",
              type: "success",
            });
            this.adddialogClose();
            this.adddialogVisible = false;
          }
          // console.log(res.data);
        });
      this.getData(this.page, this.offset);
    },
    editcar() {
      this.$axios
        .post("admin/update_car", this.$qs.stringify(this.car))
        .then((res) => {
          if (res.data.status === 0) {
            this.$message({
              message: "修改成功！",
              type: "success",
            });
            this.editdailgVisible = false;
          }
          // console.log(res.data);
        });
    },
    adddialogClose() {
      this.$refs.addFormRef.resetFields();
      this.resetCarForm();
    },
    editdaillogClose() {
      this.$refs.editFormRef.resetFields();
    },
    resetCarForm() {
      this.carForm.carname = "";
      this.carForm.carbrand = "";
      this.carForm.carstock = "";
      this.carForm.carimg = "";
      this.carForm.carprice = "";
      this.carForm.carmodel = "";
      this.carForm.carnote = "";
    },

    //库存数量增减
    addRow(row) {
      //   console.log(row)
      row.carstock = row.carstock + 1;
    },
    delRow(row) {
      //   console.log(row)
      if (row.carstock != 0) {
        row.carstock = row.carstock - 1;
      }
    },
  },
  data() {
    return {
      fileList:[],
      tableData: [],
      car: {},
      page: 1,
      offset: 5,
      total: 10,
      editdailgVisible: false,
      adddialogVisible: false,
      carForm: {
        carname: "",
        carbrand: "",
        carstock: "",
        carimg: "",
        carprice: "",
        carmodel: "",
        carnote: "",
      },
    };
  },
};
</script>

<style>
.el-table-column {
  margin-bottom: 20px;
}
/* el-menu {
    border-right: solid 2px #0c0c0c;
    padding-left: 0;
    background-color: rgb(240, 30, 30);
} */
</style>
