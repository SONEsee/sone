<template>
  <div>
    <v-form>
      <v-container>
        <v-row>
          <v-col cols="12" sm="6"
            ><v-text-field
              outlined
              label="ຊື່"
              prepend-inner-icon="mdi-account"
            ></v-text-field>
          </v-col>
          <v-col cols="12" sm="6"
            ><v-text-field
              outlined
              label="ນາມສະກຸນ"
              prepend-inner-icon="mdi-account"
            ></v-text-field>
          </v-col>

          <v-col cols="12" sm="4"
            ><v-text-field
              outlined
              label="ວັນເກີດ"
              prepend-inner-icon="mdi-cake-variant-outline"
            ></v-text-field>
          </v-col>

          <v-col sm="4" cols="12"
            ><v-text-field
              outlined
              label="ເດືອນເກິດ"
              prepend-inner-icon="mdi-cake-variant-outline"
            ></v-text-field>
          </v-col>

          <v-col cols="12" sm="4"
            ><v-text-field
              outlined
              label="ປີເກິດ"
              prepend-inner-icon="mdi-cake-variant-outline"
            ></v-text-field>
          </v-col>

          <v-col cols="12" sm="4"
            ><v-text-field
              outlined
              label="ບ້ານປະຈຸບັນ"
              prepend-inner-icon="mdi-map-marker"
            ></v-text-field>
          </v-col>

          <v-col sm="4" cols="12"
            ><v-text-field
              outlined
              label="ເມືອງປະຈຸບັນ"
              prepend-inner-icon="mdi-map-marker"
            ></v-text-field>
          </v-col>

          <v-col cols="12" sm="4"
            ><v-text-field
              outlined
              label="ແຂວງຢູປະຈຸບັນ"
              prepend-inner-icon="mdi-map-marker"
            ></v-text-field>
          </v-col>

          <v-col cols="12"
            ><v-text-field
              outlined
              label="ອາຊີບ"
              prepend-inner-icon="mdi-map-marker"
            ></v-text-field>
          </v-col>
          <v-col cols="12"
            ><v-text-field
              outlined
              label="ຕຳແໜງ"
              prepend-inner-icon="mdi-map-marker"
            ></v-text-field>
          </v-col>
        </v-row>
        <v-file-input
          cols="12"
          :rules="rules"
          accept="image/png, image/jpeg, image/bmp"
          placeholder="Pick an avatar"
          prepend-icon="mdi-camera"
          label="Avatar"
        ></v-file-input>
      </v-container>
    </v-form>
  </div>
</template>

<script>
export default {
  data: () => ({
    first: 'John',
    last: 'Doe',
  }),
}
</script>

<style></style>
