<template>
    <div>
        <nav class="sb-topnav navbar navbar-expand navbar-dark bg-dark">
            <!-- Navbar Brand-->
            <a class="navbar-brand ps-3" href="index.html">Bill Library</a>
            <!-- Sidebar Toggle-->
            <button class="btn btn-link btn-sm order-1 order-lg-0 me-4 me-lg-0" id="sidebarToggle" href="#!"><i class="fas fa-bars"></i></button>
            <!-- Navbar Search-->
            <form class="d-none d-md-inline-block form-inline ms-auto me-0 me-md-3 my-2 my-md-0">
                <div class="input-group">
                    <input class="form-control" type="text" placeholder="Search for..." aria-label="Search for..." aria-describedby="btnNavbarSearch" />
                    <button class="btn btn-primary" id="btnNavbarSearch" type="button"><i class="fas fa-search"></i></button>
                </div>
            </form>
            <!-- Navbar-->
            <ul class="navbar-nav ms-auto ms-md-0 me-3 me-lg-4">
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" id="navbarDropdown" href="#" role="button" data-bs-toggle="dropdown" aria-expanded="false"><i class="fas fa-user fa-fw"></i></a>
                    <ul class="dropdown-menu dropdown-menu-end" aria-labelledby="navbarDropdown">
                        <li><a class="dropdown-item" href="/vue_perpus/#/Login" @click="Logout">Logout</a></li>
                    </ul>
                </li>
            </ul>
        </nav>
        <div id="layoutSidenav">

            <div id="layoutSidenav_nav">
                <nav class="sb-sidenav accordion sb-sidenav-dark" id="sidenavAccordion">
                    <div class="sb-sidenav-menu">
                        <div class="nav">
                            <router-link to="/home" class="nav-link">
                                <div class="sb-nav-link-icon"><i class="fas fa-solid fa-home"></i></div>
                                Dashboard
                            </router-link>
                            <div class="sb-sidenav-menu-heading">Master Data</div>
                            <router-link to="/book" class="nav-link">
                                <div class="sb-nav-link-icon"><i class="fas fa-solid fa-book"></i></div>
                                Buku
                            </router-link>
                            <router-link to="/book_borrowing" class="nav-link">
                                <div class="sb-nav-link-icon"><i class="fas fa-solid fa-book"></i></div>
                                Peminjaman Buku
                            </router-link>
                            <router-link to="/class1" class="nav-link">
                                <div class="sb-nav-link-icon"><i class="fa fa-university"></i></div>
                                Kelas
                            </router-link>
                             <router-link to="/student" class="nav-link">
                                <div class="sb-nav-link-icon"><i class="fa fa-user"></i></div>
                                Siswa
                            </router-link>
                        </div>
                    </div>
                    <div class="sb-sidenav-footer">
                        <div class="small">Logged in as:</div>
                        User Youth Library
                    </div>
                </nav>
            </div>

            <div id="layoutSidenav_content">
                <main>
                    <!-- INI NANTI DINAMIS -->
                    <router-view></router-view>
                </main>
                
                <footer class="py-4 bg-light mt-auto">
                    <div class="container-fluid px-4">
                        <div class="d-flex align-items-center justify-content-between small">
                            <div class="text-muted">Copyright &copy; Your Website 2021</div>
                            <div>
                                <a href="#">Privacy Policy</a>
                                &middot;
                                <a href="#">Terms &amp; Conditions</a>
                            </div>
                        </div>
                    </div>
                </footer>
            </div>
        </div>
    </div>
</template>
<script>
module.exports = {
    methods: {
        Logout: function(){
            //menghapus cookies token
           // this.$cookies.remove('Authorization')
            localStorage.removeItem('Authorization')
            location.reload()
        }
    }

}
</script>