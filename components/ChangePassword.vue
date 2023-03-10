<template>
  <div>
    <a-modal title="Change password" :visible="visible" @cancel="$emit('close')">
        <a-form-model :model="form" :rules="rules" ref="ruleForm">
            <a-form-model-item label="Password" prop="password">
                <a-input-password v-model="form.password"></a-input-password>
            </a-form-model-item>
            <a-form-model-item label="Confirm Password" prop="confirmPass">
                <a-input-password v-model="form.confirmPassword"></a-input-password>
            </a-form-model-item>
        </a-form-model>
        <template slot="footer">
            <a-button @click="$emit('close')">Cancel</a-button>
            <a-button key="submit" type="primary" @click="submit">Save</a-button>
        </template>
    </a-modal>
  </div>
</template>

<script>
export default {
    props: ['visible', 'form'],
    data(){
        return{
            rules:{

            }
        }
    },
    methods:{ 
        submit(){
            if(!this.form.password || !this.form.confirmPassword){
                this.$notification.error({
                    message: 'Error',
                    description: 'Invalid'
                })
            } else {
                this.$emit('submit', this.form)
            }
            
        }
    }
}
</script>

<style>

</style>