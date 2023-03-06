<template>
  <div>
    <a-modal :visible="visible" @cancel="$emit('close')">
        <a-form-model>
            
        </a-form-model>
        <template slot="footer">
            <a-button key="back" @click="$emit('close')">
                Close
            </a-button>
            <a-button key="submit" type="primary" :loading="loading" @click="handleOk">Submit</a-button>
        </template>
    </a-modal>
    
  </div>
</template>

<script>
export default {
    props: ['visible', 'form'],

}
</script>

<style>

</style>