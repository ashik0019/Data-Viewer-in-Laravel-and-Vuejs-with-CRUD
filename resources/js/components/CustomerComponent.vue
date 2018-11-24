<template>
    <div id="customer">
        <div class="row justify-content-center">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                        <h4 class="card-title float-left">Customer</h4>
                        <h4 class="card-tools float-right">
                            <button @click="reload" class="btn btn-primary">Reload <i class="fas fa-sync"></i></button>
                        </h4>

                    </div>
                    <div class="card-body">
                        <div class="mb-3">
                            <div class="row">
                                <div class="col-md-2">
                                    <strong>Search By: </strong>
                                </div>
                                <div class="col-md-3">
                                    <select v-model="queryField"  id="fields" class="form-control">
                                        <option value="name">Name</option>
                                        <option value="email">Email</option>
                                        <option value="phone">Phone</option>
                                        <option value="address">Address</option>
                                        <option value="total">Total</option>
                                    </select>
                                </div>
                                <div class="col-md-7">
                                    <input v-model="query" type="text" class="form-control" placeholder="Search">
                                </div>
                            </div>
                        </div>
                        <div class="table-responsive">
                            <table class="table table-hover table-bordered table-striped">
                                <thead>
                                <tr>
                                    <th scope="col">#</th>
                                    <th scope="col">Name</th>
                                    <th scope="col">Email</th>
                                    <th scope="col">Phone</th>
                                    <th scope="col">Total</th>
                                    <th scope="col" class="text-center">Action</th>
                                </tr>
                                </thead>
                                <tbody>
                                <tr v-show="customers.length" v-for="(customer, index) in customers" :key="customer.id">
                                    <th scope="row">{{index + 1}}</th>
                                    <td>{{customer.name}}</td>
                                    <td>{{customer.email}}</td>
                                    <td>{{customer.phone}}</td>
                                    <td>{{customer.total}}</td>
                                    <td class="text-center">
                                        <button type="button" class="btn btn-info btn-sm">
                                            <i class="fas fa-eye"></i>
                                        </button>
                                        <button type="button" class="btn btn-primary btn-sm">
                                            <i class="fas fa-edit"></i>
                                        </button>
                                        <button type="button" class="btn btn-danger btn-sm">
                                            <i class="fas fa-trash-alt"></i>
                                        </button>
                                    </td>
                                </tr>
                                <tr v-show="!customers.length">
                                    <td colspan="6">
                                        <div class="alert alert-danger">
                                            sorry :( No data Found !.
                                        </div>
                                    </td>
                                </tr>
                                </tbody>
                            </table>
                            <pagination v-if="pagination.last_page > 1"
                                        :pagination="pagination"
                                        :offset="5"
                                        @paginate="query === '' ? getData() : searchData()"
                            ></pagination>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <vue-progress-bar></vue-progress-bar>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                query: '',
                queryField: 'name',
                customers: [],
                pagination: {
                    current_page: 1,
                }
            }
        },
        watch: {
            query: function (newQ, old) {
                if(newQ === ''){
                    this.getData();
                }else {
                    this.searchData()
                }
            }
        },
        mounted() {
            console.log('Component mounted.')
            this.getData()
        },
        methods: {
            getData(){
                this.$Progress.start()
                axios.get('/api/customers?page='+this.pagination.current_page)
                .then(response => {
                    this.customers = response.data.data
                    this.pagination = response.data.meta
                    this.$Progress.finish()
                })
                .catch(e => {
                    console.log(e)
                    this.$Progress.fail()
                })
            },
            searchData(){
                this.$Progress.start()
                axios.get('/api/search/customers/'+this.queryField+'/'+this.query+'?page='+this.pagination.current_page)
                .then(response => {
                    this.customers = response.data.data
                    this.pagination = response.data.meta
                    this.$Progress.finish()
                })
                .catch(e => {
                    console.log(e)
                    this.$Progress.fail()
                })
            },
            reload(){
                this.getData()
                this.query = ''
                this.queryField = 'name'
            }
        }
    }
</script>
