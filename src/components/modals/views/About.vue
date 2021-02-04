<template>
<el-dialog :title="lang.modal.about.title" ref="fmModal" :visible.sync="showModal" width="30%" :before-close="hideModal">

    <div class="modal-body">
        <strong>{{ lang.modal.about.name }}</strong>
        <hr>
        <dl class="row">
            <dt class="col-3">{{ lang.modal.about.version }}:</dt>
            <dd class="col-9">{{ version }}</dd>

            <dt class="col-3">{{ lang.modal.about.developer }}:</dt>
            <dd class="col-9">Aleksandr Manekin (alexusmai@gmail.com)</dd>

            <dt class="col-3">GitHub:</dt>
            <dd class="col-9">
                <a href="https://github.com/alexusmai/laravel-file-manager" target="_blank">
                    alexusmai/laravel-file-manager
                </a>
                <br>
                <a href="https://github.com/alexusmai/vue-laravel-file-manager" target="_blank">
                    alexusmai/vue-laravel-file-manager
                </a>
            </dd>
        </dl>
    </div>

    <div slot="footer" class="dialog-footer">
        <el-button type="danger" v-on:click="hideModal">{{ lang.btn.cancel }}</el-button>
    </div>

</el-dialog>
</template>

<script>
import modal from '../mixins/modal';
import translate from '../../../mixins/translate';

export default {
    name: 'About',
    props: ['showModal'],
    mixins: [modal, translate],
    computed: {
        /**
         * App version
         * @returns {*}
         */
        version() {
            return this.$store.state.fm.settings.version;
        },
    },
    methods: {
        hideModal() {
            this.$store.commit('fm/modal/clearModal');
        },
    }
};
</script>
