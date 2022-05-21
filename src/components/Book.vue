<template>
    <main>
        <div class="container-fluid px-4">
            <h2 class="mt-4">Buku</h2>
            <ol class="breadcrumb mb-4">
                <li class="breadcrumb-item"><router-link to="/home">Dashboard</router-link></li>
                <li class="breadcrumb-item active">Buku</li>
            </ol>

            <div class="card mb-4">
                <div class="card-header">
                    <i class="fas fa-table me-1"></i>
                    List Buku

                    <button @click="Add()" data-bs-toggle="modal" data-bs-target="#book_modal" class="btn btn-primary btn-sm float-end"><i class="fas fa-plus fa-fw"></i> Tambah Buku</button>
                </div>
                <div class="card-body">
                    <table id="book_table" class="table table-responsive table-striped table-hover">
                        <thead>
                            <tr>
                                <th>No</th>
                                <th>Cover Buku</th>
                                <th>Nama Buku</th>
                                <th>Pengarang</th>
                                <th>Deskripsi</th>
                                <th>Aksi</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(lb, index) in list_book" :key="lb.id_book">
                                <td>{{ index+1 }}</td>
                                <td>
                                    <img v-if="lb.image !== null" :src="api_url2 + '/images/' + lb.image" width="150">
                                    <button v-else class="btn btn-sm btn-warning" @click="Edit(lb)" data-bs-toggle="modal" data-bs-target="#book_cover_modal" ><i class="fas fa-image fa-fw"></i></button>
                                </td>
                                <td>{{ lb.book_name }}</td>
                                <td>{{ lb.author }}</td>
                                <td>{{ lb.description }}</td>
                                <td>
                                    <button class="btn btn-sm btn-info" @click="Edit(lb)" data-bs-toggle="modal" data-bs-target="#book_modal" ><i class="fas fa-pencil-alt fa-fw"></i></button>&nbsp;
                                    <button class="btn btn-sm btn-danger" @click="Delete(lb.id_book)"><i class="fas fa-trash-alt fa-fw"></i></button>
                                </td>
                            </tr>
                            
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="book_modal" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="modal-dialog modal-md bg-primary">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="staticBackdropLabel">Data Buku</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="mb-3">
                            <label for="book_name" class="form-label">Nama Buku</label>
                            <input type="text" class="form-control" id="book_name" v-model="book_name" placeholder="Nama Buku">
                        </div>

                        <div class="mb-3">
                            <label for="author" class="form-label">Pengarang</label>
                            <input type="text" class="form-control" id="author" v-model="author" placeholder="Pengarang">
                        </div>

                        <div class="mb-3">
                            <label for="description" class="form-label">Deskripsi</label>
                            <textarea class="form-control" id="description" v-model="description" rows="3"></textarea>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                        <button type="button" class="btn btn-primary" @click="Save()" data-bs-dismiss="modal">Submit</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="book_cover_modal" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="modal-dialog modal-sm">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="staticBackdropLabel">Cover Buku</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">

                        <div class="mb-3">
                            <label for="book_cover" class="form-label">Cover Buku</label>
                            <input type="file" class="form-control" id="book_cover" @change="uploadCover($event)">
                        </div>

                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                        <button type="button" class="btn btn-primary" @click="Upload(id_book)" data-bs-dismiss="modal">Submit</button>
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
            id_book: "",
            book_name: "",
            author: "",
            description: "",
            book_cover: "",
            action: "",
            list_book: [],
        }
    },
    methods: {
        getData: function(){
            //token
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }
            axios.get(api_url + "/book", token)
            .then( response => {
                if(response.data.status !== 0){
                    this.list_book = response.data;
                } else {
                    this.$store.commit('logout')
                    this.$router.push('/login')
                }
                
            })
        },
        Add: function() {
            this.id_book = ""
            this.book_name = ""
            this.author = ""
            this.description = ""
            this.action = "insert"
        },
        Edit: function(lb){
            this.id_book = lb.id_book
            this.book_name = lb.book_name
            this.author = lb.author
            this.description = lb.description
            this.action = "update"
        },
        uploadCover: function(e){
            this.book_cover = e.target.files[0]
        },
        Upload: function(id){
            let token = {
                headers : { 
                    "Authorization" : "Bearer " + localStorage.getItem("Authorization"), 
                    'Content-Type' : 'multipart/form-data',
                }
            }

            let form  = new FormData()
            form.append("book_cover", this.book_cover)

            axios.post(api_url + '/book/UploadBookCover/'+ id, form, token)
            .then( response => {
                Swal.fire({
                    title: 'Success !',
                    text: response.data.message,
                    icon: 'success',
                    confirmButtonText: 'OK'
                })

                this.getData()
            })

        },
        Save: function() {
            //mapping header token
            let token = {
                headers : { 
                    "Authorization" : "Bearer " + localStorage.getItem("Authorization")
                }
            }

            //mapping data
            let form  = {
                //backend       //state
                'book_name': this.book_name,
                'author': this.author,
                'description': this.description,
                'book_cover' : this.book_cover
            }

            if(this.action === 'insert'){ //POST

                axios.post(api_url + '/book', form, token)
                .then( response => {
                   Swal.fire({
                        title: 'Success !',
                        text: response.data.message,
                        icon: 'success',
                        confirmButtonText: 'OK'
                    })
                })

            } else { //PUT
                axios.put(api_url + '/book/' + this.id_book, form, token)
                .then( response => {
                    Swal.fire({
                        title: 'Success !',
                        text: response.data.message,
                        icon: 'success',
                        confirmButtonText: 'OK'
                    })
                })
            }

            this.getData()
        },
        Delete: function(id_book){
            //mapping header token
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }

            Swal.fire({
                title: 'Delete Book Data',
                text: 'Are you sure you delete this data ?',
                icon: 'warning',
                showDenyButton: true,
                showCancelButton: false,
                confirmButtonText: 'Yes',
                denyButtonText: `No`,
            }).then((result) => {
                if (result.isConfirmed) {
                    axios.delete(api_url + '/book/' + id_book, token)
                    .then( response => {

                        Swal.fire({
                            title: 'Success !',
                            text: response.data.message,
                            icon: 'success',
                            confirmButtonText: 'OK'
                        })

                        this.getData()
                    })

                    //Swal('Saved!', '', 'success')
                } else if (result.isDenied) {
                    Swal.fire({
                        title: 'Cancel !',
                        text: 'Data is not deleted',
                        icon: 'error',
                        confirmButtonText: 'OK'
                    })
                }
            })

        }
    },
    created(){
        this.getData()
    },
}
</script>