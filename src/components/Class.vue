<template>
    <main>
        <div class="container-fluid px-4">
            <h2 class="mt-4">Kelas</h2>
            <ol class="breadcrumb mb-4">
                <li class="breadcrumb-item"><router-link to="/home">Dashboard</router-link></li>
                <li class="breadcrumb-item active">Kelas</li>
            </ol>

            <div class="card mb-4">
                <div class="card-header">
                    <i class="fas fa-table me-1"></i>
                    List Kelas

                    <button @click="Add()" data-bs-toggle="modal" data-bs-target="#class_modal" class="btn btn-primary btn-sm float-end"><i class="fas fa-plus fa-fw"></i> Tambah Kelas</button>
                </div>
                <div class="card-body">
                    <table id="class_table" class="table table-responsive table-striped table-hover">
                        <thead>
                            <tr>
                                <th>No</th>
                                <th>Kelas</th>
                                <th>Aksi</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(lb, index) in list_class" :key="lb.id_class">
                                <td>{{ index+1 }}</td>
                                <td>{{ lb.class_name }}</td>
                                <td>
                                    <button class="btn btn-sm btn-info" @click="Edit(lb)" data-bs-toggle="modal" data-bs-target="#class_modal" ><i class="fas fa-pencil-alt fa-fw"></i></button>&nbsp;
                                    <button class="btn btn-sm btn-danger" @click="Delete(lb.id_class)"><i class="fas fa-trash-alt fa-fw"></i></button>
                                </td>
                            </tr>
                            
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="class_modal" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="modal-dialog modal-md bg-primary">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="staticBackdropLabel">Data Kelas</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="mb-3">
                            <label for="class_name" class="form-label">Kelas</label>
                            <input type="text" class="form-control" id="class_name" v-model="class_name" placeholder="Nama Kelas">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                        <button type="button" class="btn btn-primary" @click="Save()" data-bs-dismiss="modal">Submit</button>
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
            id_class: "",
            class_name: "",
            action: "",
            list_class: [],
        }
    },
    methods: {
        getData: function(){
            //token
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }
            axios.get(api_url +  "/class1", token)
            .then( response => {
                if(response.data.status !== 0){
                    this.list_class = response.data;
                } else {
                    this.$store.commit('logout')
                    this.$router.push('/login')
                }
                
            })
        },
        Add: function() {
            this.id_class = ""
            this.class_name = ""
            this.action = "insert"
        },
        Edit: function(lb){
            this.id_class = lb.id_class
            this.class_name = lb.class_name
            this.action = "update"
        },
        Upload: function(id){
            let token = {
                headers : { 
                    "Authorization" : "Bearer " + localStorage.getItem("Authorization"), 
                    'Content-Type' : 'multipart/form-data',
                }
            }
                this.getData()

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
                'class_name': this.class_name,
            }

            if(this.action === 'insert'){ //POST

                axios.post(api_url + '/class1', form, token)
                .then( response => {
                   Swal.fire({
                        title: 'Success !',
                        text: response.data.message,
                        icon: 'success',
                        confirmButtonText: 'OK'
                    })
                })

            } else { //PUT
                axios.put(api_url + '/class1/' + this.id_class, form, token)
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
        Delete: function(id_class){
            //mapping header token
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }

            Swal.fire({
                title: 'Delete Class Data',
                text: 'Are you sure you delete this data ?',
                icon: 'warning',
                showDenyButton: true,
                showCancelButton: false,
                confirmButtonText: 'Yes',
                denyButtonText: `No`,
            }).then((result) => {
                if (result.isConfirmed) {
                     axios.delete(api_url +  '/class1/' + id_class, token)
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