<template>
    <div class="navbar">
        <!--<span>Upload New Data</span>
        <span>View Uploaded Data</span>-->
    </div>
</template>

<script>
export default {
  name: 'NavBar',
  props: {
    val: String
  }
}</script>


<style scoped>
      nav {
        width: 100%;
        height: 40px;
        text-align: left;
        position: relative;
    }
</style>
