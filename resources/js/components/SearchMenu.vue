<template>
    <div class="w-3/5" v-if="layouts">

        <div v-if="this.limitCounter != 0">
            <div v-if="layouts.length === 1">
                <button
                    dusk="toggle-layouts-dropdown-or-add-default"
                    type="button"
                    tabindex="0"
                    class="btn btn-default btn-primary inline-flex items-center relative float-left"
                    @click="toggleLayoutsDropdownOrAddDefault"
                >
                    <span>{{ field.button }}</span>
                </button>
            </div>
            <div v-if="layouts.length > 1">
                <div style="min-width: 300px;">
                    <div
                        class="flexible-search-menu-multiselect"
                        :class="{
                            'mb-4': addAtPosition
                        }"
                    >
                        <multiselect
                            v-model="selectedLayout" :options="layouts"
                             :custom-label="renderLayoutName"
                             :placeholder="field.button"
                             @input="selectLayout"
                             v-bind="attributes"
                             track-by="name"
                        ></multiselect>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

    import Multiselect from 'vue-multiselect'
    import { eventBus } from '../eventbus';

    export default {
        components: {Multiselect},

        props: ['layouts', 'field', 'resourceName', 'resourceId', 'resource', 'errors', 'limitCounter', 'addAtPosition', 'index'],

        data() {
            return {
                selectedLayout: null,
                isLayoutsDropdownOpen: false
            };
        },

        computed: {
            attributes() {
                return {
                    selectLabel: this.field.menu.data.selectLabel || __('Press enter to select'),
                    label: this.field.menu.data.label || 'title',
                    openDirection: this.field.menu.data.openDirection || 'bottom',
                }
            }
        },

        methods: {
            selectLayout(value){
                this.addGroup(value);
            },
            renderLayoutName(layout){
                return layout.title;
            },
            /**
             * Display or hide the layouts choice dropdown if there are multiple layouts
             * or directly add the only available layout.
             */
            toggleLayoutsDropdownOrAddDefault(event) {
                if (this.layouts.length === 1) {
                    return this.addGroup(this.layouts[0]);
                }

                this.isLayoutsDropdownOpen = !this.isLayoutsDropdownOpen;
            },

            /**
             * Add the given layout to flexible content's list at button's position
             */
            addGroup(layout) {
                if (!layout) return;

                eventBus.$emit('add-group-'+this.field.attribute, layout, this.index);

                this.isLayoutsDropdownOpen = false;
                this.selectedLayout = null;
            },
        }
    }
</script>
