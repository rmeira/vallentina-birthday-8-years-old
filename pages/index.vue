<template>
  <v-main class="secondary">
    <v-img
      :src="background"
      width="100%"
      class="d-flex align-end text-center"
      gradient="to top right, rgba(142,100,201,.3), rgba(0,0,0,.0)"
    >
      <h1 class="display-3 font-weight-bold text-uppercase accent--text">Vallen</h1>
    </v-img>
    <v-sheet color="transparent" class="title text-center pa-2 pt-6" >
      Você está convidado para um dia cheio de aventuras e encanto em meu
      aniversário!
    </v-sheet>
    <v-sheet width="100%" class="d-flex justify-center" color="transparent">
      <v-avatar size="140" class="elevation-2">
        <v-img src="/icon.jpg"> </v-img>
      </v-avatar>
    </v-sheet>
    <v-sheet color="primary" width="100%" class="mt-6 pa-6 mb-0">
      <v-dialog v-model="dialog" width="500" class="rounded-xl">
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            x-large
            color="accent"
            class=" black--text"
            v-bind="attrs"
            rounded
            block
            v-on="on"
          >
            Confirmar presença
          </v-btn>
        </template>
        <v-form ref="presenceFormRef" @submit.prevent="handleSubmitForm()">
          <v-card class="rounded-xl" light>
            <v-toolbar color="accent" height="80" elevation="0">
              <v-toolbar-title class="font-weight-bold">
                Confirme sua presença
              </v-toolbar-title>
              <v-spacer />
              <v-btn icon @click="dialog = false">
                <v-icon> mdi-close </v-icon>
              </v-btn>
            </v-toolbar>

            <v-card-text class="pt-6">
              <v-text-field
                placeholder="Informe o nome dos familiares"
                height="64"
                rounded
                outlined
                v-model="form.family_name"
                :rules="rules.required"
              />
              <v-text-field
                placeholder="Quantidade adultos"
                type="number"
                :rules="rules.required"
                height="64"
                rounded
                outlined
                min="0"
                v-model.number="form.adults"
                hint="Somente números"
              />
              <v-text-field
                placeholder="Quantidade Crianças"
                type="number"
                min="0"
                height="64"
                rounded
                outlined
                v-model.number="form.children"
                hint="Somente números"
              />
              <v-text-field
                placeholder="Informe seu celular"
                height="64"
                rounded
                outlined
                v-mask="['(##) ####-####', '(##) #####-####']"
                v-model="form.phone"
                :rules="rules.required"
                type="tel"
                hint="Somente números"
              />
            </v-card-text>
            <v-divider></v-divider>
            <v-card-actions class="pa-4">
              <v-spacer></v-spacer>
              <v-btn color="accent" rounded large type="submit" width="200" class=" black--text">
                Confirmar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-form>
      </v-dialog>
    </v-sheet>
    <v-sheet
      color="transparent"
      width="100%"
      class="pa-6 subtitle-1 font-weight-bold"
    >
      Esperamos vocês no dia 14 de novembro as 19h30 no Buffet Viva Vida Granja
      Vianna, confira o endereço abaixo
    </v-sheet>
    <v-sheet color="transparent" width="100%" class="pb-0">
      <iframe
        src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d58505.97616050401!2d-46.841863580762336!3d-23.581961551044383!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x94cfaab38182880f%3A0xe2f0f8380f95c3ad!2sBuffet%20Viva%20Vida!5e0!3m2!1spt-BR!2sbr!4v1665010044922!5m2!1spt-BR!2sbr"
        width="100%"
        height="450"
        style="border: 0"
        allowfullscreen=""
        loading="lazy"
        referrerpolicy="no-referrer-when-downgrade"
      ></iframe>
    </v-sheet>
  </v-main>
</template>

<script>
import background from '~/assets/background.jpeg'
import validateMixin from '~/mixins/validateMixin'

const presencePayload = {
  event_id: 3,
  family_name: null,
  adults: null,
  children: null,
  phone: null,
}

export default {
  data() {
    return {
      background,
      dialog: false,
      form: {
        ...presencePayload,
      },
      successSend: false,
    }
  },
  mixins: [validateMixin],
  methods: {
    async handleSubmitForm() {
      if (this.$refs.presenceFormRef.validate()) {
        this.$nuxt.$loading.start()
        await this.$axios
          .post('/api/presences', this.form)
          .then((response) => {
            if (response) {
              this.form = { ...presencePayload }
              this.dialog = false
              this.$refs.presenceFormRef.resetValidation()
              this.$toast.success('Presença confirmada com sucesso!')
            }
          })
          .catch(() => {
            this.$toast.error(
              'Ops, nao foi possivel confirmar sua presenca, por favor verifique os dados informados.'
            )
          })
        this.$nuxt.$loading.finish()
      }
    },
  },
}
</script>
