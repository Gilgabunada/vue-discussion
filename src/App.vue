<script setup>
import Navbar from './components/NavBar.vue'
import About from './components/About.vue'
import Project from './components/Project.vue'
import Tools from './components/Tools.vue'
import Contacts from './components/Contacts.vue'
import Footer from './components/Footer.vue'
</script>

<template>
    <Navbar />
    <div class="container-fluid">
        <About />
        <Project />
        <Tools />
        <Contacts />
    </div>
    <Footer />
</template>
