<template>
    <FormSection :label="'Search ' + label" :index="index">
        <div class="row form-group">
            <div class="col-md-3">
                <label :for="index" class="ms-3">
                    <strong>{{ 'Search ' + label }}:</strong>
                </label>
            </div>
            <div class="col-md-5">
                <select
                :id="index"
                :name="index"
                :ref="index"
                class="form-control"
            />
            </div>
        </div>
    </FormSection>
</template>

<script>
import { v4 as uuid } from 'uuid'
import FormSection from './FormSection.vue'

export default {
    name: 'Select2Search',
    props: {
        label: {
            type: String,
            required: true,
        },
        lookupApiEndpoint: {
            type: String,
            required: true,
        },
        redirectPath: {
            type: String,
            required: true,
        },
        theme: {
            type: String,
            default: 'bootstrap-5',
        },
    },
    components: {
        FormSection,
    },
    data:function () {
        return {
            email_user: null,
            uuid: uuid(),
            index: 'search-' + this.label.toLowerCase().replace(' ', '-'),
        }
    },
    methods: {
        initialiseLookup: function(){
            let vm = this;
            $(`#${vm.index}`).select2({
                minimumInputLength: 2,
                'theme': vm.theme,
                allowClear: true,
                placeholder:'Select ' + vm.label,
                ajax: {
                    url: vm.lookupApiEndpoint,
                    dataType: 'json',
                    data: function(params) {
                        console.log(params)
                        var query = {
                            term: params.term,
                            type: 'public',
                        }
                        return query;
                    },
                },
            }).
            on('select2:open', function (e) {
                const searchField = $(`[aria-controls='select2-${vm.index}-results']`)
                searchField[0].focus();
            }).
            on('select2:select', function (e) {
                var selected = $(e.currentTarget);
                window.location = vm.redirectPath + selected.val();
            });
        },
    },
    mounted: function () {
        this.initialiseLookup();
    },
}
</script>
