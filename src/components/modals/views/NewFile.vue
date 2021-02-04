<template>
<el-dialog :title="lang.modal.newFile.title" ref="fmModal" :visible.sync="showModal" width="30%" :before-close="hideModal">

    <div class="form-group">
        <label for="fm-file-name">{{ lang.modal.newFile.fieldName }}</label>
        <el-input type="text" class="form-control" id="fm-file-name" v-focus v-bind:class="{'is-invalid': fileExist}" v-model="fileName" v-on:keyup="validateFileName"></el-input>
        <div class="invalid-feedback" v-show="fileExist">
            {{ lang.modal.newFile.fieldFeedback }}
        </div>
    </div>

    <div slot="footer" class="dialog-footer">
        <el-button type="info" v-bind:disabled="!submitActive" v-on:click="addFile">{{ lang.btn.submit }}
        </el-button>
        <el-button type="danger" v-on:click="hideModal">{{ lang.btn.cancel }}</el-button>
    </div>

</el-dialog>
</template>

<script>
import modal from '../mixins/modal';
import translate from '../../../mixins/translate';

export default {
    name: 'NewFile',
    props: ['showModal'],
    mixins: [modal, translate],
    data() {
        return {
            // name for new file
            fileName: '',

            // file exist
            fileExist: false,
        };
    },
    computed: {
        /**
         * Submit button - active or no
         * @returns {string|boolean}
         */
        submitActive() {
            return this.fileName && !this.fileExist;
        },
    },
    methods: {
        /**
         * Check the file name if it exists or not.
         */
        validateFileName() {
            if (this.fileName) {
                this.fileExist = this.$store.getters[`fm/${this.activeManager}/fileExist`](this.fileName);
            } else {
                this.fileExist = false;
            }
        },

        /**
         * Create new file
         */
        addFile() {
            this.$store.dispatch('fm/createFile', this.fileName).then((response) => {
                // if new directory created successfully
                if (response.data.result.status === 'success') {
                    // close modal window
                    this.hideModal();
                }
            });
        },
        hideModal() {
            this.$store.commit('fm/modal/clearModal');
        },
    },
};
</script>
