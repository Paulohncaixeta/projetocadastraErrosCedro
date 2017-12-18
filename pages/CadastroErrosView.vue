<template>
<div>
  <v-layout justify-center align-center>
    <v-flex xs11 sm8>
      <div class="text-xs-center">
        <img src="/static/logo-cedro.png" alt="CedroTech" class="mb-5" />
        <h2>Cadastro de Erros</h2>
      </div>
      <v-card>
        <v-card-text>
          <v-form v-model="valid" ref="form" lazy-validation>
            <v-text-field
              label="Titulo"
              v-model="titulo"
              :rules="nameRules"
              :counter="10"
              required
            ></v-text-field>
            <v-select
              label="Selecione o Produto"
              v-model="select"
              :items="items"
              :rules="[v => !!v || 'Nome do produto é necessário']"
              required
            ></v-select>
            <v-card-text>
              <v-flex xs12>
                <v-text-field
                  name="input-1"
                  label="Descrição"
                  textarea
                ></v-text-field>
              </v-flex>
            </v-card-text>
             <v-text-field
              label="Nome"
              v-model="nome"
              :rules="nameRules"
              :counter="10"
              required
            ></v-text-field>
            <v-text-field
              label="E-mail"
              v-model="email"
              :rules="emailRules"
              required
            ></v-text-field>
            <v-menu
              lazy
              :close-on-content-click="false"
              v-model="menu"
              transition="scale-transition"
              offset-y
              full-width
              :nudge-right="40"
              max-width="290px"
              min-width="290px"
              ><v-text-field
                slot="activator"
                label="Selecione a data"
                v-model="dateFormatted"
                prepend-icon="event"
                @blur="date = parseDate(dateFormatted)"
              ></v-text-field>
              <v-date-picker v-model="date" @input="dateFormatted = formatDate($event)" no-title scrollable actions>
                <template slot-scope="{ save, cancel }">
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn flat color="primary" @click="cancel">Cancel</v-btn>
                    <v-btn flat color="primary" @click="save">OK</v-btn>
                  </v-card-actions>
                </template>
              </v-date-picker>
            </v-menu>
              <v-text-field
                slot="activator"
                label="Picker in menu"
                v-model="time"
                prepend-icon="access_time"
                readonly
              ></v-text-field>
              <v-spacer></v-spacer>
              <v-dialog
                persistent
                v-model="modal2"
                lazy
                full-width
                width="290px"
                ><v-text-field
                      slot="activator"
                      label="Marque o horário em que ocorreu o problema"
                      v-model="time"
                      prepend-icon="access_time"
                      readonly
                ></v-text-field>
                    <v-time-picker v-model="time" actions>
                      <template slot-scope="{ save, cancel }">
                        <v-card-actions>
                          <v-btn flat color="primary" @click="cancel">Cancelar</v-btn>
                          <v-btn flat color="primary" @click="save">Salvar</v-btn>
                        </v-card-actions>
                      </template>
                    </v-time-picker>
              </v-dialog>
              <div>
                <v-text-field prepend-icon="attach_file" single-line
                          v-model="filename" :label="label"
                          @click.native="onFocus"
                          :disabled="disabled" ref="fileTextField"></v-text-field>
                <input type="file" :accept="accept" :multiple="false" :disabled="disabled"
                  ref="fileInput" @change="onFileChange">
              </div>
            <v-btn
              @click="submit"
              :disabled="!valid"
            >Enviar
            </v-btn>
            <v-btn @click="clear">Limpar</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</div> 
</template>

<script>
import axios from 'axios';

export default {

  data() {
    return {
      posts: [],
      errors: []
    }
  
  },
 
  created() {
    var config = {
      headers:{
        'Access-Control-Allow-Origin':'*'
      }
    }
    axios.get('http://dev.people.com.ai/bug/api/v3/products', config)
    .then(response => {
    
      this.posts = response.data
    })
    .catch(e => {
      this.errors.push(e)
    })
    console.log(this.posts)

  }
}
</script>

<script>
  export default {
    data: () => ({
      valid: true,
      titulo: '',
      tituloRules: [
        (v) => !!v || 'Titulo é necessário',
        (v) => v && v.length <= 10 || 'Name must be less than 10 characters'
      ],
      name: '',
      nameRules: [
        (v) => !!v || 'Nome é necessário',
        (v) => v && v.length <= 10 || 'Name must be less than 10 characters'
      ],
      email: '',
      emailRules: [
        (v) => !!v || 'E-mail é necessário',
        (v) => /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'Digite um e-mail válido'
      ],
      select: null,
      items: [
        'Item 1',
        'Item 2',
        'Item 3',
        'Item 4'
      ],
      checkbox: false,
      date: null,
      dateFormatted: null,
      menu: false,
      time: null,
        menu2: false,
        modal2: false
    }),
    methods: {
      submit () {
        if (this.$refs.form.validate()) {
            axios.post('/api/submit', {
            titulo:this.titulo,
            select: this.select,
            nome: this.nome,
            email: this.email,
            checkbox: this.checkbox
          })
        }
      },
      clear () {
        this.$refs.form.reset()
      },
      formatDate (date) {
        if (!date) {
          return null
        }
        const [day,month,year] = date.split('-')
        return `${day}/${month}/${year}`
      },
      parseDate (date) {
        if (!date) {
          return null
        }
        const [day,month,year] = date.split('/')
        return `${day.padStart(2, '0')}-${month.padStart(2, '0')}-${year}`
      }
    }
  }
</script>

<script>
    export default{
        props: {
            value: {
                type: [Array, String]
            },
            accept: {
                type: String,
                default: "*"
            },
            label: {
                type: String,
                default: "Escolha uma imagem do erro (.jpg ou .png)"
            },
            required: {
                type: Boolean,
                default: false
            },
            disabled: {
                type: Boolean,
                default: false
            },
            multiple: {
                type: Boolean, // not yet possible because of data
                default: false
            }
        },
        data(){
            return {
                filename: ""
            };
        },
        watch: {
            value(v){
                this.filename = v;
            }
        },
        mounted() {
            this.filename = this.value;
        },

        methods: {
            getFormData(files){
                const data = new FormData();
                [...files].forEach(file => {
                    data.append('data', file, file.name); // currently only one file at a time
                });
                return data;
            },
            onFocus(){
                if (!this.disabled) {
                    debugger;
                    this.$refs.fileInput.click();
                }
            },
            onFileChange($event){
                const files = $event.target.files || $event.dataTransfer.files;
                const form = this.getFormData(files);
                if (files) {
                    if (files.length > 0) {
                        this.filename = [...files].map(file => file.name).join(', ');
                    } else {
                        this.filename = null;
                    }
                } else {
                    this.filename = $event.target.value.split('\\').pop();
                }
                this.$emit('input', this.filename);
                this.$emit('formData', form);
            }
        }
    };
</script>
<style scoped>
    input[type=file] {
        position: absolute;
        left: -99999px;
    }
</style>
