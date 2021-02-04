<template>
<el-dialog :title="lang.modal.status.title" ref="fmModal" :visible.sync="showModal" width="30%" :before-close="hideModal">

    <div class="modal-body">
        <div v-if="errors.length">
            <ul class="list-unstyled">
                <li v-for="(item, index) in errors" v-bind:key="index">
                    {{ item.status }} - {{ item.message }}
                </li>
            </ul>
        </div>
        <div v-else>
            <span>{{ lang.modal.status.noErrors }}</span>
        </div>
    </div>

    <div slot="footer" class="dialog-footer">
        <el-button type="primary" v-bind:disabled="!errors.length" v-on:click="clearErrors">{{ lang.btn.clear }}</el-button>
        <el-button type="danger" v-on:click="hideModal">{{ lang.btn.cancel }}</el-button>
    </div>

</el-dialog>
</template>

<script>
import modal from '../mixins/modal';
import translate from '../../../mixins/translate';

export default {
    name: 'Status',
    props: ['showModal'],
    mixins: [modal, translate],
    computed: {
        /**
         * Application errors
         * @returns {default.computed.errors|(function())|Array|boolean}
         */
        errors() {
            return this.$store.state.fm.messages.errors;
        },
    },
    methods: {
        /**
         * Clear all errors
         */
        clearErrors() {
            this.$store.commit('fm/messages/clearErrors');
        },
    },
};
</script>
