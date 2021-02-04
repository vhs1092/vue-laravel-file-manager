<template>
<el-dialog :title="lang.modal.newFolder.title" ref="fmModal" :visible.sync="showModal" width="30%" :before-close="hideModal">

    <div class="form-group">
        <label for="fm-folder-name">{{ lang.modal.newFolder.fieldName }}</label>
        <el-input type="text" class="form-control" id="fm-folder-name" v-focus v-bind:class="{'is-invalid': directoryExist}" v-model="directoryName" v-on:keyup="validateDirName"></el-input>
        <div class="invalid-feedback" v-show="directoryExist">
            {{ lang.modal.newFolder.fieldFeedback }}
        </div>
    </div>

    <div slot="footer" class="dialog-footer">
        <el-button type="info" v-bind:disabled="!submitActive" v-on:click="addFolder">{{ lang.btn.submit }}
        </el-button>
        <el-button type="danger" v-on:click="hideModal">{{ lang.btn.cancel }}</el-button>
    </div>

</el-dialog>
</template>

<script>
import modal from '../mixins/modal';
import translate from '../../../mixins/translate';

export default {
    name: 'NewFolder',
    props: ['showModal'],
    mixins: [modal, translate],
    data() {
        return {
            // name for new directory
            directoryName: '',

            // directory exist
            directoryExist: false,
        };
    },
    computed: {
        /**
         * Submit button - active or no
         * @returns {string|boolean}
         */
        submitActive() {
            return this.directoryName && !this.directoryExist;
        },
    },
    methods: {
        /**
         * Check the folder name if it exists or not.
         */
        validateDirName() {
            if (this.directoryName) {
                this.directoryExist = this.$store.getters[`fm/${this.activeManager}/directoryExist`](this.directoryName);
            } else {
                this.directoryExist = false;
            }
        },

        /**
         * Create new directory
         */
        addFolder() {
            this.$store.dispatch('fm/createDirectory', this.directoryName).then((response) => {
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
