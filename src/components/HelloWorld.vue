<template>
    <div>
        <Card :bordered="false" style="margin-bottom:12px;">
            <Form ref="formInline"  :label-width="80">
                <Row>
                    <Col span='3'>
                        <FormItem label="公司名称">
                            <Input type="text" v-model="com.name" placeholder="公司名称"></Input>
                        </FormItem>
                    </Col>
                    <Col span='3'>
                        <FormItem label="公司地址">
                            <Input type="text" v-model="com.address" placeholder="公司地址"></Input>
                        </FormItem>
                    </Col>
                    <Col span='2'>
                        <FormItem label="距离">
                            <Input type="text" v-model="com.distance" placeholder="距离"></Input>
                        </FormItem>
                    </Col>
                    <Col span='2'>
                        <FormItem label="公司规模">
                            <Input type="text" v-model="com.scale" placeholder="公司规模"></Input>
                        </FormItem>
                    </Col>
                    <Col span='3'>
                        <FormItem label="薪资">
                            <Input type="text" v-model="com.salary" placeholder="薪资"></Input>
                        </FormItem>
                    </Col>
                    <Col span='5'>
                        <FormItem label="要求">
                            <Input type="textarea" v-model="com.requirement" placeholder="要求"></Input>
                        </FormItem>
                    </Col>
                    <Col span='3'>
                        <FormItem label="标签">
                            <Input type="text" v-model="com.tags" placeholder="标签"></Input>
                        </FormItem>
                    </Col>
                    <Col span='1'>
                        <Button type="primary" @click="addCom">整</Button>
                    </Col>
                    <Col span='1'>
                        <div class="up-down" @click="up">
                            <Icon type="md-arrow-round-up" />
                        </div>
                    </Col>
                    <Col span='1'>
                        <div class="up-down" @click="down">
                            <Icon type="md-arrow-round-down" />
                        </div>
                    </Col>
                </Row>
            </Form>
        </Card>
        <Card>
            <Table :columns="columns" :data="data1" :height='tableH' border stripe highlight-row @on-current-change='nothing' @on-row-click='selectRow' :draggable='true' @on-drag-drop='drag'></Table>
        </Card>
    </div>
</template>

<script>
    export default {
        data(){
            return{
                editIndex : -1,
                tableH : 200,
                activeRowIndex : null,
                com : {
                    name : '',
                    address : '',
                    distance : '',
                    scale : '',
                    salary : '',
                    requirement : '',
                    tags : ''
                },
                columns: [
                    {
                        type: 'index',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '公司名称',
                        align : 'center',
                        key: 'name'
                    },
                    {
                        title: '公司地址',
                        align : 'center',
                        key: 'address'
                    },
                    {
                        title: '距离',
                        align : 'center',
                        width : 100,
                        key: 'distance'
                    },
                    {
                        title: '公司规模',
                        align : 'center',
                        width : 150,
                        key: 'scale'
                    },
                    {
                        title: '薪资',
                        align : 'center',
                        width : 150,
                        key: 'salary'
                    },
                    {
                        title: '要求',
                        align : 'center',
                        key: 'requirement',
                        width : 700,
                        render : (h,p) =>{
                            return h('pre',{
                                domProps : {
                                    innerHTML : p.row.requirement
                                },
                                style : {
                                    'text-align' : 'left',
                                    'word-break' : 'break-all',
                                    'word-wrap' : 'wrap',
                                    'white-space':'pre-wrap',
                                    'padding-top' : '15px',
                                    'padding-bottom' : '15px'
                                }
                            })
                        }
                    },
                    {
                        title: '标签',
                        align : 'center',
                        key: 'tags',
                        render : (h,p) =>{
                            return h('div',
                                Array.apply(null, p.row.tags.split(/[,，\s]/).map(function(item){
                                        return h('Tag',item)
                                    })
                                )
                            )
                        }
                    },
                    {
                        title: '操作',
                        align : 'center',
                        width : 150,
                        key: 'do',
                        render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {
                                        type: 'primary',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: () => {
                                            this.eidt(params.index)
                                        }
                                    }
                                }, '编辑'),
                                h('Button', {
                                    props: {
                                        type: 'error',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.remove(params.index)
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                    }
                ],
                data1: []
            }
        },
        methods : {
            nothing(){},
            drag(i1,i2){
                let tem_item = this.data1[i1]
                this.data1.splice(i1,1,this.data1[i2])
                this.data1.splice(i2,1,tem_item)
                this.saveData()
            },
            up(){
                if (this.activeRowIndex) {
                    let activeItem = this.data1.splice(this.activeRowIndex,1)
                    this.data1.splice(this.activeRowIndex -1,0,activeItem[0])
                    this.activeRowIndex--
                    this.saveData()
                }
            },
            down(){
                if (this.activeRowIndex < this.data1.length -1) {
                    let activeItem = this.data1.splice(this.activeRowIndex,1)
                    this.data1.splice(this.activeRowIndex +1,0,activeItem[0])
                    this.activeRowIndex++
                    this.saveData()
                }
            },
            selectRow(currentRow,index){
                console.log(currentRow)
                console.log(index)
                this.activeRowIndex = index
            },
            eidt(index){
                this.editIndex = index
                this.com = {...this.data1[index]}
            },
            remove(index){
                console.log('remove:'+index)
                this.$Modal.confirm({
                    title: '警告',
                    content: '是否确认删除：'+this.data1[index].name,
                    onOk: () => {
                        this.data1.splice(index,1)
                        this.saveData()
                    },
                    onCancel: () => {}
                });
            },
            saveData(){
                window.localStorage.data1 = JSON.stringify(this.data1)
            },
            addCom(){
                if (this.editIndex == -1) {
                    console.log(this.com)
                    this.data1.push({
                        ...this.com
                    })
                    this.saveData()
                    this.com = {
                        name : '',
                        address : '',
                        distance : '',
                        scale : '',
                        salary : '',
                        requirement : '',
                        tags : ''
                    }
                }else{
                    this.data1.splice(this.editIndex,1,this.com)
                    this.saveData()
                    this.com = {
                        name : '',
                        address : '',
                        distance : '',
                        scale : '',
                        salary : '',
                        requirement : '',
                        tags : ''
                    }
                    this.editIndex = -1
                }
            }
        },
        mounted () {
            this.$nextTick(() => {
                    let h = document.documentElement.clientHeight || document.body.clientHeight
                    this.tableH = h - 152;
            });
        },
        created(){
            if (window.localStorage.hasOwnProperty('data1')) {
                this.data1 = JSON.parse(window.localStorage.data1)
                console.log(window.localStorage.data1)
            }
        }
    }
</script>

<style scoped>
    .up-down{
        width: 50px;
        height: 50px;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 18px;
        cursor: pointer;
    }
    .up-down:hover{
        background-color: #f6f6f6;
    }
</style>
