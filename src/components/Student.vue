<template>
    <main>
        <div class="container-fluid px-4">
            <h2 class="mt-4">Siswa</h2>
            <ol class="breadcrumb mb-4">
                <li class="breadcrumb-item"><router-link to="/home">Dashboard</router-link></li>
                <li class="breadcrumb-item active">Siswa</li>
            </ol>

            <div class="card mb-4">
                <div class="card-header">
                    <i class="fas fa-table me-1"></i>
                    List Siswa

                    <button @click="Add()" data-bs-toggle="modal" data-bs-target="#student_modal" class="btn btn-primary btn-sm float-end"><i class="fas fa-plus fa-fw"></i> Tambah Siswa</button>
                </div>
                <div class="card-body">
                    <table id="student_table" class="table table-responsive table-striped table-hover">
                        <thead>
                            <tr>
                                <th>No</th>
                                <th>Foto Siswa</th>
                                <th>Nama Siswa</th>
                                <th>Tanggal Lahir</th>
                                <th>Jenis Kelamin</th>
                                <th>Alamat</th>
                                <th>Kelas</th>
                                <th>Aksi</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(ls, index) in list_student" :key="ls.id_student">
                                <td>{{ index+1 }}</td>
                                <td>
                                    <img v-if="ls.image !== null" :src="api_url2 + '/images/' + ls.image" width="150">
                                    <button v-else class="btn btn-sm btn-warning" @click="Edit(ls)" data-bs-toggle="modal" data-bs-target="#student_cover_modal" ><i class="fas fa-image fa-fw"></i></button>
                                </td>
                                <td>{{ ls.student_name }}</td>
                                <td>{{ ls.date_birth }}</td>
                                <td>
                                    <span class="badge bg-info" v-if="ls.gender === 'L'">Laki-Laki</span>
                                    <span class="badge bg-warning" v-if="ls.gender === 'P'">Perempuan</span>
                                </td>
                                <td>{{ ls.address }}</td>
                                <td><span class="badge bg-dark" >{{ ls.class_name }}</span></td>
                                <td>
                                    <button class="btn btn-sm btn-info" @click="Edit(ls)" data-bs-toggle="modal" data-bs-target="#student_modal" ><i class="fas fa-pencil-alt fa-fw"></i></button>
                                    <button class="btn btn-sm btn-danger" @click="Delete(ls.id_student)"><i class="fas fa-trash-alt fa-fw"></i></button>
                                </td>
                            </tr>
                            
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="student_modal" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="modal-dialog modal-md bg-primary">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="staticBackdropLabel">Data Siswa</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <div class="mb-3">
                            <label for="student_name" class="form-label">Nama Siswa</label>
                            <input type="text" class="form-control" id="student_name" v-model="student_name" placeholder="Nama Siswa">
                        </div>
                        <div class="mb-3">
                            <label for="date_birth" class="form-label">Tanggal Lahir</label>
                            <input type="date" class="form-control" id="date_birth" v-model="date_birth" placeholder="Tanggal Lahir">
                        </div>

                        <div class="mb-3">
                            <label for="gender" class="form-label">Jenis Kelamin</label>
                            <select class="form-control" v-model="gender">
                                <option value="" disabled>-- Pilih Jenis Kelamin --</option>
                                <option value="L">Laki-Laki</option>
                                <option value="P">Perempuan</option>
                            </select>
                        </div>

                        <div class="mb-3">
                            <label for="address" class="form-label">Alamat</label>
                            <textarea class="form-control" id="address" v-model="address" rows="3"></textarea>
                        </div>

                        <div class="mb-3">
                            <label for="class1" class="form-label">Kelas</label>
                            <select class="form-control" v-model="id_class">
                                <option value="" disabled>-- Choose Class --</option>
                                <option v-for="c in list_class" :key="c" v-bind:value="c.id_class">{{ c.class_name }}</option>
                            </select>
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
        <div class="modal fade" id="student_cover_modal" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="modal-dialog modal-sm">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="staticBackdropLabel">Foto Siswa</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">

                        <div class="mb-3">
                            <label for="student_cover" class="form-label">Foto Siswa</label>
                            <input type="file" class="form-control" id="student_cover" @change="uploadCover($event)">
                        </div>

                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
                        <button type="button" class="btn btn-primary" @click="Upload(id_student)" data-bs-dismiss="modal">Submit</button>
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
            student_cover: "",
            student_name: "",
            date_birth: "",  
            gender: "",
            address: "",
            action: "",
            id_class: "",
            list_student: [],
            list_class: []
        }
    },
    methods: {
        getData: function(){
            //token
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }
            axios.get(api_url + "/student", token)
            .then( response => {
                if(response.data.status !== 0){
                    this.list_student = response.data;
                } else {
                    this.$store.commit('logout')
                    this.$router.push('/login')
                }
                
            })
        },
        getClass: function(){
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }
            axios.get(api_url + "/class1", token)
            .then( response => {
                this.list_class = response.data;
            })
        },
        Add: function() {
            this.id_student = ""
            this.student_name = ""
            this.date_birth = ""
            this.gender = ""
            this.address = ""
            this.id_class = ""
            this.action = "insert"

            this.getClass()
        },
        Edit: function(ls){
            this.id_student = ls.id_student
            this.student_name = ls.student_name
            this.date_birth = ls.date_birth
            this.gender = ls.gender
            this.address = ls.address
            this.id_class = ls.id_class
            this.action = "update"

            this.getClass()
        },
        uploadCover: function(e){
            this.student_cover = e.target.files[0]
        },
        Upload: function(id){
            let token = {
                headers : { 
                    "Authorization" : "Bearer " + localStorage.getItem("Authorization"), 
                    'Content-Type' : 'multipart/form-data',
                }
            }

            let form  = new FormData()
            form.append("student_cover", this.student_cover)

            axios.post(api_url + '/student/UploadStudentCover/'+ id, form, token)
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
            let token = {
                headers : { 
                    "Authorization" : "Bearer " + localStorage.getItem("Authorization")
                }
            }

            let form  = {
                'student_name': this.student_name,
                'date_birth': this.date_birth,
                'gender': this.gender,
                'address': this.address,
                'id_class': this.id_class,
                'student_cover' : this.student_cover,
            }

            if(this.action === 'insert'){ //POST

                axios.post(api_url + '/student', form, token)
                .then( response => {
                   Swal.fire({
                        title: 'Success !',
                        text: response.data.message,
                        icon: 'success',
                        confirmButtonText: 'OK'
                    })
                })

            } else { //PUT
                axios.put(api_url + '/student/' + this.id_student, form, token)
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
        Delete: function(id_student){
            //mapping header token
            let token = {
                headers : { "Authorization" : "Bearer " + localStorage.getItem("Authorization")}
            }

            Swal.fire({
                title: 'Delete Student Data',
                text: 'Are you sure you delete this data ?',
                icon: 'warning',
                showDenyButton: true,
                showCancelsutton: false,
                confirmButtonText: 'Yes',
                denyButtonText: `No`,
            }).then((result) => {
                if (result.isConfirmed) {
                    axios.delete(api_url + '/student/' + id_student, token)
                    .then( response => {

                        Swal.fire({
                            title: 'Success !',
                            text: response.data.message,
                            icon: 'success',
                            confirmButtonText: 'OK'
                        })

                        this.getData()
                    })
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