<!-- Vue component -->
<template>
    <div id="vuecity">
        <transition name="fade">
            <autocomplete
                    :source="urlEndpoint"
                    :results-property="result_property"
                    @selected="addElement"
                    :inputClass="input_class"
                    :placeholder="setPlaceholder"
                    :results-display="formatDisplay">
            </autocomplete>
        </transition>
        <template v-if="multiple === true">
            <transition-group name="fade" class="results">
                <template v-for="(value, index) in this.listShow ">
                <span v-bind:key="index" class="tag dynamic"
                      @click="removeElement(index)">{{value[1]}}  ( {{value[0]}} )
                <span v-bind:key="index" class="close">
                    <img v-bind:key="index" src="../assets/times-solid.svg" class="autocomplete__icon">
                </span> </span>
                </template>
            </transition-group>
        </template>
        <template v-else>
            <template v-if="this.listShow.length > 0">
                  <span class="tag dynamic"
                        @click="removeElement(index)">{{this.listShow[1]}}  ( {{this.listShow[0]}} )
                <span class="close">
                    <img src="../assets/times-solid.svg" class="autocomplete__icon">
                </span> </span>
            </template>
        </template>

        <input type="hidden" :name="label"  :value="selectedData">
    </div>
</template>

<script>
    import Autocomplete from "vuejs-auto-complete";

    export default {
        name: 'vue-city-auto',
        components: {
            Autocomplete
        },
        props: {
            data: {
                type: String,
                default: 'https://api-adresse.data.gouv.fr/search/?q=',
            },
            label: {
                type: String,
                default: '',
            },
            input_class: null,
            result_property: {
                type: String,
                default: 'features',
            },
            data_to_get: null,
            size_font: null,
            multiple: Boolean,
        },
        data() {
            return {
                value: [],
                listShow: [],
                select: 'Appuyer sur entrée pour choisir',
                deselect: 'Appuyer sur entrée pour annuler',
                selected: "Sélectionné",
            }
        },
        mounted() {
            // console.log(this.data);
        },
        methods: {
            urlEndpoint(input) {
                return this.data + input
            },
            formatDisplay(result) {
                return result.properties.label + ', ' + result.properties.postcode
            },
            addElement(element) {
                var data_to_grab = '';

                if (this.data_to_get) {
                    const propertie = element.selectedObject.properties[this.data_to_get];
                    data_to_grab = (propertie) ? propertie : '';
                }

                if (this.multiple !== undefined) {
                    if (this.multiple === true) {
                        this.listShow.push([data_to_grab,
                            element.selectedObject.properties.label]);
                        this.value.push(data_to_grab)
                    } else {
                        this.listShow = [data_to_grab,
                            element.selectedObject.properties.label];
                        this.value = data_to_grab;
                    }
                } else {
                    this.listShow = [data_to_grab,
                        element.selectedObject.properties.label];
                    this.value = data_to_grab;
                }

            },
            removeElement(elementId) {
                if (this.multiple) {
                    if (this.multiple === true) {
                        this.listShow.splice(elementId, 1);
                        this.value.splice(elementId, 1);
                    } else {
                        this.listShow = [];
                        this.value = [];
                    }
                } else {
                    this.listShow = [];
                    this.value = [];
                }

            },
        },
        computed: {
            selectedData: function () {
                let selectedUsers = [];
                if (this.multiple) {
                    if (this.multiple === true) {
                        Object.keys(this.value).forEach(key => {
                            let val = this.value[key];// value of the current key
                            if (typeof (val) == 'object') {
                                Object.keys(val).forEach(key => {
                                    let value = val[key];// value of the current key
                                    selectedUsers.push(value);
                                });
                            } else {
                                selectedUsers.push(val);
                            }
                        });
                    } else {
                        selectedUsers.push(this.value);
                    }
                }
                else {
                    selectedUsers.push(this.value);
                }

                this.$emit('input', selectedUsers);
                return selectedUsers;
            },
            cssProps() {
                return {
                    '--font-size': (this.size_font) + "rem"
                }
            },
            setPlaceholder() {
                if (typeof this.value === 'object') {
                    return "Chercher une adresse";
                } else {
                    if (this.value !== '' || this.value.length !== 0) {
                        return this.value;
                    } else {
                        return "Chercher une adresse";
                    }
                }
            }
        }
    }
</script>
<style>
    .multiselect, .multiselect__input, .multiselect__single {
        font-size: var(--font-size);
    }

    #vuecity {
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
    }

    span.tag {
        display: block;
        float: left;
        padding: 5px 9px;
        text-decoration: none;
        background: white;
        color: #aaaaaa !important;
        border: 1px solid #aaaaaa;
        margin-right: 7px;
        font-weight: 500;
        margin-bottom: 0px;
        border-radius: 10px;
    }

    span.dynamic {
        cursor: pointer;
        margin: 0.5rem;
        padding: 4px 10px;
    }

    span.dynamic:hover {
        background-color: #cb0a3c;
        color: white !important;
    }

    .fade-enter-active, .fade-leave-active {
        transition: opacity .25s;
    }

    .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */
    {
        opacity: 0;
    }


</style>
