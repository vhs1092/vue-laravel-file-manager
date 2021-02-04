<template>
<el-dialog :title="lang.modal.delete.title" ref="fmModal" :visible.sync="showModal" width="30%" :before-close="hideModal">

    <div class="modal-body">
        <div v-if="selectedItems.length">
            <selected-file-list />
        </div>
        <div v-else>
            <span class="text-danger">{{ lang.modal.delete.noSelected }}</span>
        </div>
    </div>

    <div slot="footer" class="dialog-footer">
        <el-button type="danger" v-on:click="deleteItems">{{ lang.modal.delete.title }}
        </el-button>
        <el-button type="danger" v-on:click="hideModal">{{ lang.btn.cancel }}</el-button>
    </div>
</el-dialog>
</template>

<script>
import SelectedFileList from '../additions/SelectedFileList.vue';
import modal from '../mixins/modal';
import translate from '../../../mixins/translate';

export default {
    name: 'Delete',
    props: ['showModal'],

    mixins: [modal, translate],
    components: {
        SelectedFileList
    },
    computed: {
        /**
         * Files and folders for deleting
         * @returns {*}
         */
        selectedItems() {
            return this.$store.getters['fm/selectedItems'];
        },
    },
    methods: {
        /**
         * Delete selected directories and files
         */
        deleteItems() {
            // create items list for delete
            const items = this.selectedItems.map((item) => ({
                path: item.path,
                type: item.type,
            }));

            this.$store.dispatch('fm/delete', items).then(() => {
                // close modal window
                this.hideModal();
            });
        },
    },
};
</script>
