<template>
    <v-container fluid>
        <v-layout>
            <v-flex>
                <v-layout column class="ma-3">
                    <h1 class="headline">Filtrar Usuário</h1>
                    <v-divider class="mb-3" />
                    <div v-if="erros">
                        <Erros :erros="erros" />
                    </div>
                    <v-text-field label="ID"
                        v-model.number="filtro.id" />
                    <v-text-field label="E-mail"
                        v-model="filtro.email" />

                    <h1 class="mt-4 headline">Alterar Usuário</h1>
                    <v-divider class="mb-3" />
                    <v-text-field label="Nome"
                        v-model="usuario.nome" />
                    <v-text-field label="E-mail"
                        v-model="usuario.email" />
                    <v-text-field label="Senha"
                        v-model="usuario.senha" type="password" />
                    <v-select label="Perfis"
                        v-model="usuario.perfis"
                        :items="perfis"
                        item-value="id"
                        item-text="rotulo"
                        attach multiple
                        chips deletable-chips />
                    <v-btn class="ml-0 mt-3"
                        @click="obterPerfis">
                        Obter Perfis
                    </v-btn>
                    <v-btn color="primary" class="ml-0 mt-3"
                        @click="alterarUsuario">
                        Alterar Usuário
                    </v-btn>
                </v-layout>
            </v-flex>
            <v-flex>
                <v-layout column class="ma-3">
                    <h1 class="headline">Resultado</h1>
                    <v-divider />
                    <template v-if="dados">
                        <v-text-field label="ID" readonly
                            v-model="dados.id" />
                        <v-text-field label="Nome" readonly
                            v-model="dados.name" />
                        <v-text-field label="E-mail" readonly
                            v-model="dados.email" />
                        <v-text-field label="Perfis" readonly
                            :value="perfisRotulos" />
                    </template>
                </v-layout>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
import Erros from '../comum/Erros'
import gql from 'graphql-tag'

export default {
    components: { Erros },
    data() {
        return {
            filtro: {},
            usuario: {},
            perfis: [],
            dados: null,
            erros: null
        }
    },
    computed: {
        perfisRotulos() {
            return this.dados && this.dados.profiles &&
                this.dados.profiles.map(p => p.role).join(', ')
        },
        perfisSelecionados() {
            if(this.usuario.profiles) {
                return this.usuario.profiles.map(id => ({ id }))
            } else {
                return null
            }
        }
    },
    methods: {
        alterarUsuario() {
            // this.$api.mutate({
            //     mutation: gql`
            //         mutation(
            //             $id: ID
            //             $email: String
            //             $name: String
            //             $email: String
            //             $password: String
            //             $profiles: [ProfileFilters]
            //         ){
            //             updateUser(
            //                 data: {
            //                     name: $name
            //                     email: $email
            //                     password: $password
            //                     profiles: $profiles
            //                 }
            //                 filters: {
            //                     id: $idFilter
            //                     email: $emailFilter
            //                 }
            //             ) {
            //                 id name email profiles { role }
            //             }
            //         }
            //     `,
            //     variables: {
            //        data: {
            //             name: this.usuario.nome,
            //             email: this.usuario.email,
            //             password: this.usuario.password,
            //             profiles: this.perfisSelecionados
            //        },
            //        filters: {
            //            id: this.filtro.id,
            //            email: this.filtro.email
            //        }
            //     }
            // }).then(response => {
            //     this.dados = response.data.updateUser
            //     this.filtro = {}
            //     this.usuario = {}
            //     this.erros = null
            // }).catch(e => {
            //     this.erros = e
            // })
        },
        obterPerfis() {
            this.$api.query({
                query: gql`{ profiles { id role }}`
            })
            .then(response => {
                return response.data.profiles.map(profile => ({ id: profile.id, rotulo: profile.role }))
            })
            .then(profiles => {
                this.perfis = [...profiles]
                this.error = null
            }).catch(e => this.erros = e)
        }
    }
}
</script>

<style>

</style>
