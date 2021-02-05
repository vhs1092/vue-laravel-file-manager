<template>
    <div class="fm-table">
        <table class="table table-sm">
            <thead>
                <tr>
                    <th class="w-auto" v-if="files.length > 0"></th>
                    <th class="w-65" v-on:click="sortBy('name')">
                        {{ lang.manager.table.name }}
                        <template v-if="sortSettings.field === 'name'">
                            <i class="far fa-sort-amount-down"
                               v-show="sortSettings.direction === 'down'"/>
                            <i class="far fa-sort-amount-up"
                               v-show="sortSettings.direction === 'up'"/>
                        </template>
                    </th>
                    <th class="w-10" v-on:click="sortBy('size')">
                        {{ lang.manager.table.size }}
                        <template v-if="sortSettings.field === 'size'">
                            <i class="far fa-sort-amount-down"
                               v-show="sortSettings.direction === 'down'"/>
                            <i class="far fa-sort-amount-up"
                               v-show="sortSettings.direction === 'up'"/>
                        </template>
                    </th>
                    <th class="w-10" v-on:click="sortBy('type')">
                        {{ lang.manager.table.type }}
                        <template v-if="sortSettings.field === 'type'">
                            <i class="far fa-sort-amount-down"
                               v-show="sortSettings.direction === 'down'"/>
                            <i class="far fa-sort-amount-up"
                               v-show="sortSettings.direction === 'up'"/>
                        </template>
                    </th>
                    <th class="w-auto" v-on:click="sortBy('date')">
                        {{ lang.manager.table.date }}
                        <template v-if="sortSettings.field === 'date'">
                            <i class="far fa-sort-amount-down"
                               v-show="sortSettings.direction === 'down'"/>
                            <i class="far fa-sort-amount-up"
                               v-show="sortSettings.direction === 'up'"/>
                        </template>
                    </th>
                </tr>
            </thead>
            <tbody>

                <tr v-if="!isRootPath">
                    <td colspan="4" class="fm-content-item" v-on:click="levelUp">
                        <i class="far fa-level-up-alt"/>
                    </td>
                </tr>
                <tr v-for="(directory, index) in directories"
                    v-bind:key="`d-${index}`"
                    v-bind:class="{'table-info': checkSelect('directories', directory.path)}"
                    v-on:click="selectItem('directories', directory.path, $event)"
                    v-on:contextmenu.prevent="contextMenu(directory, $event)">
                    <td class="fm-content-item unselectable"
                        v-bind:class="(acl && directory.acl === 0) ? 'text-hidden' : ''"
                        v-on:dblclick="selectDirectory(directory.path)">
                        <i class="far fa-folder"/> {{ directory.basename }}
                    </td>
                    <td/>
                    <td>{{ lang.manager.table.folder }}</td>
                    <td>
                        {{ timestampToDate(directory.timestamp) }}
                    </td>
                </tr>
                <tr v-for="(file, index) in files"
                    v-bind:key="`f-${index}`"
                    v-bind:class="{'table-info': checkSelect('files', file.path)}"
                    v-on:click="selectItem('files', file.path, $event)"
                    v-on:dblclick="selectAction(file.path, file.extension)"
                    v-on:contextmenu.prevent="contextMenu(file, $event)">
                    <td><el-checkbox @change.native="confirm_send_files(file)"
                        align="center"></el-checkbox></td>
                    <td class="fm-content-item unselectable"
                        v-bind:class="(acl && file.acl === 0) ? 'text-hidden' : ''">
                        <i class="far" v-bind:class="extensionToIcon(file.extension)"/>
                        {{ file.filename ? file.filename : file.basename }}
                    </td>
                    <td>{{ bytesToHuman(file.size) }}</td>
                    <td>
                        {{ file.extension }}
                    </td>
                    <td>
                        {{ timestampToDate(file.timestamp) }}
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import translate from '../../mixins/translate';
import helper from '../../mixins/helper';
import managerHelper from './mixins/manager';
import EventBus from '../../eventBus';
export default {
  name: 'table-view',
  mixins: [translate, helper, managerHelper],
  props:{
    manager: { type: String, required: true }
  },
  computed: {
    /**
     * Sort settings
     * @returns {*}
     */
    sortSettings() {
      return this.$store.state.fm[this.manager].sort;
    },
  },
  data() {
    return {
        filesToSend:[],
        filesToAttach:[]
    };
  },
  methods: {
    /**
     * Sort by field
     * @param field
     */
    sortBy(field) {
      this.$store.dispatch(`fm/${this.manager}/sortBy`, { field, direction: null });
    },
    /***
     * Files to send through email
     */
    confirm_send_files(file){

        const result = this.filesToSend.find( ({ file_path }) => file_path === file.path );
        if(result !== undefined){
            this.filesToSend = this.filesToSend.filter(function(el) { return el.file_path != file.path; });
        }else{
        
            let data = {};
            data.file_name = file.basename;
            data.file_path = file.path;
            data.url = file.path;
            data.folder_and_file_path = file.path;
            data.name = file.basename;
            this.filesToSend.push(data);
        }

        this.$emit('attachFilesToEmail', this.filesToSend);
    }
  },
};
</script>

<style lang="scss">
    .fm-table {

        thead th{
            background: white;
            position: sticky;
            top: 0;
            z-index: 10;
            cursor: pointer;
            border-top: none;

            &:hover {
                background-color: #f8f9fa;
            }

            & > i {
                padding-left: 0.5rem;
            }
        }

        td {
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        tr:hover {
            background-color: #f8f9fa;
        }

        .w-10 {
            width: 10%;
        }

        .w-65 {
            width: 65%;
        }

        .fm-content-item {
            cursor: pointer;
            max-width: 1px;
        }

        .text-hidden {
            color: #cdcdcd;
        }
        .table-info{
                background-color: #bee5eb;
        }
    }
</style>
