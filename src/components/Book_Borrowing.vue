<template>
    <main>
        <div class="container-fluid px-4">
            <h2 class="mt-4">Peminjaman Buku</h2>
            <ol class="breadcrumb mb-4">
                <li class="breadcrumb-item"><router-link to="/home">Dashboard</router-link></li>
                <li class="breadcrumb-item active">Peminjaman Buku</li>
            </ol>

            <div class="card mb-4">
                <div class="card-header">
                    <i class="fas fa-table me-1"></i>
                    List Peminjaman Buku

                    <button @click="Add()" data-bs-toggle="modal" data-bs-target="#borrowing_modal" class="btn btn-primary btn-sm float-end"><i class="fas fa-plus fa-fw"></i> Add Peminjaman Buku</button>
                </div>
                <div class="card-body">
                    <table id="student_table" class="table table-responsive table-striped table-hover">
                        <thead>
                            <tr>
                                <th>No</th>
                                <th>Nama Siswa</th>
                                <th>Tanggal Peminjaman</th>
                                <th>Tanggal Pengembalian</th>
                                <th>Aksi</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(ls, index) in list_transaction" :key="ls">
                                <td>{{ index+1 }}</td>
                                <td>{{ ls.student_name }}</td>
                                <td>{{ ls.borrow_date }}</td>
                                <td>{{ ls.return_date }}</td>
                                <td>
                                    <button class="btn btn-sm btn-info" @click="Detail(ls)" data-bs-toggle="modal" data-bs-target="#borrowing_detail_modal" ><i class="fas fa-list fa-fw"></i></button>&nbsp;
                                    <button class="btn btn-sm btn-success" @click="Return(ls.student.id_student)" data-bs-toggle="modal" data-bs-target="#borrowing_modal"><i class="fas fa-check fa-fw"></i></button>
                                </td>
                            </tr>
                            
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="borrowing_detail_modal" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="modal-dialog modal-lg bg-primary">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="staticBackdropLabel">Date Peminjaman Buku</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <table class="table table-responsive table-stripped">
                            <thead>
                                <tr>
                                    <th>No</th>
                                    <th>Cover Buku</th>
                                    <th>Nama Buku</th>
                                    <th>Pengarang</th>
                                </tr>
                            </thead>
 
                            <tbody>
                                <tr v-for="(detail, index) in list_detail_transaction" :key="detail">
                                    <td>{{index+1}}</td>
                                    <td>
                                        <img v-if="detail.book.image !== null" :src="api_url2 + '/images/' + detail.book.image" width="150">
                                        <span v-else>No Image</span>
                                    </td>
                                    <td>{{ detail.book.book_name }}</td>
                                    <td>{{ detail.book.author }}</td>
                                </tr>  
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="borrowing_modal" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="modal-dialog modal-xl bg-primary">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="staticBackdropLabel">Data Peminjaman Buku</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="row">
                            <div class="col-md-3">
                                <div class="card">
                                    <div class="card-body">
                                         <div class="mb-3">
                                            <label for="student" class="form-label">Siswa</label>
                                            <select class="form-control" v-model="id_student">
                                                <option value="" disabled>-- Pilih Siswa --</option>
                                                <option v-for="ls in list_student" :key="ls" v-bind:value="ls.id_student">{{ ls.student_name }}</option>
                                            </select>
                                        </div>
                                        <div class="mb-3">
                                            <label for="borrow_date" class="form-label">Tanggal Peminjaman</label>
                                            <input type="date" class="form-control" id="borrow_date" v-model="borrow_date" placeholder="Tanggal Peminjaman">
                                        </div>
                                        <div class="mb-3">
                                            <label for="return_date" class="form-label">Tanggal Pengembalian</label>
                                            <input type="date" class="form-control" id="return_date" v-model="return_date" placeholder="Tanggal Pengembalian">
                                        </div>
                                    </div>
                                </div>
                               
                            </div>
                            <div class="col-md-9">
                                <div class="card">
                                    <div class="card-body">
                                        <button @click="addItem" class="btn btn-sm btn-primary text-white"><i class="mdi mdi-plus btn-icon-prepend"></i> Tambah Item</button>
                                        <br><br>
                                        <div class="row" v-for="(detail, counter) in transaction_detail" :key="counter">
                                            <br><br>
                                            <div class="col-md-8">
                                                <div class="form-group">
                                                    <select class="form-control" v-model="detail.id_book">
                                                        <option value="" disabled>-- Pilih Buku --</option>
                                                        <option v-for="lb in list_book" :key="lb.id_book" v-bind:value="lb.id_book">{{ lb.book_name }} - {{ lb.author }}</option>
                                                    </select>
                                                </div>
                                            </div>
                                            <div class="col-md-3">
                                                <div class="form-group">
                                                    <input class="form-control" placeholder="Jumlah" type="number" v-model="detail.qty"> 
                                                </div>
                                            </div>
                                            <div class="col-md-1">
                                                <button class="btn btn-danger btn-sm" @click="deleteItem(counter)"> - </button>
                                            </div>
                                        </div>
                                    </div>
                                    
                                </div>
                                
                            </div>
                        </div>
                        
                    </div>
                    <div class="modal-footer">
                        <button @click="Save" class="btn btn-block btn-lg btn-success">Submit</button>
                    </div>
                </div>
            </div>
        </div>

    </main>
</template>
<script>
module.exports = {
    data : function(){
        return {
            id_student: "",
            borrow_date: "",
            return_date: "",
            list_transaction: [],
            list_detail_transaction: [],

            //for transaction form
            list_book: [],
            list_student: [],
            transaction_detail: [],
        }
    },
    methods: {
        addItem(){
            this.transaction_detail.push({
                id_book: '',
                qty: '',
            })
        },
        deleteItem(counter){
            this.transaction_detail.splice(counter,1);
        },
        getBook: function(){
            //ambil data book untuk dropdown
            let conf = { headers: { "Authorization" : 'Bearer ' + localStorage.getItem("Authorization") } };
            axios.get(api_url + "/book", conf)
            .then(response => {
                this.list_book = response.data;
            })
        },
        getStudent: function(){
            //ambil data student untuk dropdown
            let conf = { headers: { "Authorization" : 'Bearer ' + localStorage.getItem("Authorization") } };
            axios.get(api_url + "/student", conf)
            .then(response => {
                this.list_student = response.data;
            })
        },
        getData: function(){
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }
            axios.get(api_url + "/book_borrowing", token)
            .then( response => {
                this.list_transaction = response.data;
            })
        },
        //statusCheck: function(return_date){
            //const status = moment().isBefore(moment(return_date))
            //if(status){
                //return true
            //} else {
            //    return false
           // }
        //},
        Add: function() {
            this.id_student = ""
            this.borrow_date = ""
            this.return_date = ""

            this.getBook()
            this.getStudent()
        },

        Detail: function(data) {
            this.id_student = data.id_student
            this.borrow_date = data.borrow_date
            this.return_date = data.return_date

            //get detail
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }
            axios.get(api_url + "/book_borrowing_detail/" + data.id_book_borrowing, token)
            .then( response => {
                this.list_detail_transaction = response.data
            })
        },

        Save: function() {
            let token = {
                headers : { 
                    "Authorization" : "Bearer " + localStorage.getItem("Authorization")
                }
            }
            
            let form = {
                "id_student": this.id_student,
                "borrow_date": this.borrow_date,
                "return_date": this.return_date,
                "detail": this.transaction_detail
            }

            axios.post(api_url + '/book_borrowing', form, token)
            .then( response => {
                Swal.fire({
                    title: 'Success !',
                    text: response.data.message,
                    icon: 'success',
                    confirmButtonText: 'OK'
                })
            })

            this.getData()
        },
    },
    created(){
        this.getData()
    },
}
</script>