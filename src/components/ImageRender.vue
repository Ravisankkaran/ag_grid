<template>
    <div id="image-cellrender">
        <!-- image -->
        <img :src="params.value" alt="Product Image" style="width: 100%; height: auto;" />
    </div>
</template>
<script>
export default{
    // exporting component as prop
    name:'ImageCellRender',
    props: {
    params: Object
  }
}
</script>
<style scoped>
img{
    padding: 20px;
}
</style>