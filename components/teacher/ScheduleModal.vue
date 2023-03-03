<template>
  <div>
    <a-modal :visible="visible" @cancel="this.$emit('close')">
        <template slot="footer">
            <a-button @cancel="this.$emit('close')">
                Return
            </a-button>
            <a-button type="primary" :loading="loading" @click="handleOk">
                Submit
            </a-button>
        </template>
    </a-modal>
  </div>
</template>

<script>
export default {
    props: ['visible'],
    data(){
        return{

        }
    }
}
</script>

<style>

</style>