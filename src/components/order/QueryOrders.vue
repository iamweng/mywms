<template>
    <div>
        <!-- Card -->
        <el-card>
            <el-row :gutter="20">
                <!-- Input -->
                <el-col :span="8">  
                    <el-input placeholder="请输入内容" v-model="orderID" @clear="getOrderList" @keyup.enter.native="getOrderList" clearable>
                        <el-button slot="append" icon="el-icon-search" @click="getOrderList"></el-button>
                    </el-input>
                </el-col>
            </el-row>
            <el-table :data="orderList" stripe>
                <el-table-column type="index" label="#"></el-table-column>
                <el-table-column label="ID" prop="id"></el-table-column>
                <el-table-column label="订单编号" prop="number"></el-table-column>
                <el-table-column label="承运用户" prop="carrier_user_id"></el-table-column>
                <el-table-column label="承运车辆" prop="carrier_car_id"></el-table-column>
                <el-table-column label="货物ID" prop="goods_id"></el-table-column>
                <el-table-column label="出发城市" prop="from_city"></el-table-column>
                <el-table-column label="到达城市" prop="to_city"></el-table-column>
                <el-table-column label="是否付款" >
                    <template slot-scope="scope">
                        <el-tag v-if="scope.row.payment_status == 1" type="success">已付款</el-tag>
                        <el-tag v-if="scope.row.payment_status == 0" type="danger">未付款</el-tag>
                    </template>
                </el-table-column>
                <el-table-column label="是否发货">
                    <template slot-scope="scope">
                        <el-tag v-if="scope.row.courier_status == 1" type="success">已发货</el-tag>
                        <el-tag v-if="scope.row.courier_status == 0" type="danger">未发货</el-tag>
                    </template>
                </el-table-column>
                <el-table-column label="下单时间" prop="created_at"></el-table-column>
                <el-table-column label="操作" width="200px" >
                    <template slot-scope="scope">
                        <el-button type="primary" icon="el-icon-edit" size="mini" @click="showEditOrderDialog(scope.row)"></el-button>
                        <el-button type="success" icon="el-icon-position" size="mini" @click="showViewLogisticsDialog(scope.row)"></el-button>
                    </template>
                </el-table-column>
            </el-table>  
        </el-card>
        <!-- Edit order Dialog -->
        <!-- <el-dialog title="修改地址" :visible.sync="editOrderDialogVisible" width="40%" @close="editOrderCloseDialog">
            <el-form :model="editOrderForm" ref="editOrderFormRef" :rules="editOrderFormRules">
                <el-form-item label="省市区/县" prop="address1">
                    <el-input v-model="editOrderForm.address1"></el-input>
                </el-form-item>
                <el-form-item label="详细地址" prop="address2">
                    <el-input v-model="editOrderForm.address2"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editOrderDialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="editOrder">确 定</el-button>
            </span>
        </el-dialog> -->
        <!-- View logistics dialog -->
        <el-dialog title="物流信息" :visible.sync="viewLogisticsDialogVisible" width="40%" @close="viewLogisticsCloseDialog">
            <!-- Timeline -->
            <el-timeline>
                <el-timeline-item   v-for="(item, index) in LogisticsInfo" :key="index"   :timestamp="item.time">
                    {{item.context}}
                </el-timeline-item>
            </el-timeline>
            <span slot="footer" class="dialog-footer">                
                <el-button type="primary" @click="viewLogisticsDialogVisible = false">关 闭</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
export default {
    data() {
        return {
            orderID: null,
            orderList: [],
            orderListSize: 0,
            // editOrderDialogVisible: false,
            // editOrderFormRules: {
            //     address1: [{required: true,  message: '请填写省市区', trigger: 'blur' },],
            //     address2: [{required: true,  message: '请填写地址', trigger: 'blur' },]
            // },
            // editOrderForm: {
            //     address1: '',
            //     address2: '',
            // },
            currentOrder: {},
            viewLogisticsDialogVisible: false,
            LogisticsInfo: [],

        }
    },
    methods: {
        async getOrderList() {
            this.orderList.splice(0,this.orderList.length)
            const result = await this.$http.get('orders/' + this.orderID)
            console.log(result)
            if(result.data.code !== 200) return this.$message.error(result.data.msg)
            this.$message.success(result.data.msg)
            this.orderList.push(result.data.data)
        },
        // editOrderCloseDialog() {
        //     this.$refs.editOrderFormRef.resetFields()
        //     this.editOrderDialogVisible = false
        // },
        // showEditOrderDialog(item) {
        //     this.editOrderDialogVisible = true
        //     this.currentOrder = item
        // },
        // async editOrder() {
        //     this.$refs.editOrderFormRef.validate(async valid => {
        //         if(!valid) return
        //     // const result = await this.$http('')
        //     // if(result.data.meta.status !== 200) return this.$message.error(result.data.msg)
        //     // this.$message.success(result.data.msg)
        //     // this.getOrderList()
        //     this.editOrderDialogVisible = false
        //     })
        // },
        viewLogisticsCloseDialog() {
            this.viewLogisticsDialogVisible = false
        },
        async showViewLogisticsDialog(item) {
            this.viewLogisticsDialogVisible = true
            const result = await this.$http.get('/kuaidi/1106975712662')
            console.log(result)
            if(result.data.meta.status !== 200) return this.$message.error(result.data.msg)
            this.$message.success(result.data.msg)
            this.LogisticsInfo = result.data.data

        },
    }
}
</script>

<style scoped>

</style>