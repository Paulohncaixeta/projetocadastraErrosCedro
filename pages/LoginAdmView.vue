<template>
  <v-form v-model="valid">
    <v-text-field
      label="Nome"
      v-model="name"
      :rules="nameRules"
      :counter="10"
      required
    ></v-text-field>
    <v-text-field
      label="Senha"
      v-model="password"
      :rules="emailRules"
      required
    ></v-text-field>
  </v-form>
</template>