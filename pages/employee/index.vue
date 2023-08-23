<template>
  <div>
    <template>
      <v-card>
        <v-card-title>
          ຂໍ້ມຸນພະນັກງານ
          <v-spacer></v-spacer>
          <v-btn color="primary" dark to="/employee/create"> ເພີ່ມຂໍ້ມູນ </v-btn>
        </v-card-title>

        <v-data-table :headers="headers" :items="user_data">
          <template v-slot:item.image="{ item, index }">
            <v-card
              width="90"
              height="100"
              class="ma-1"
              elevation="0"
              color="grey lighten-4"
              tile
            >
              <v-img
                width="100%"
                height="100%"
                :src="getImage(item.image)"
              ></v-img>
            </v-card>
          </template>
          <template v-slot:item.date="{ item, index }">
            {{ formatDate(item.date_of_birth) }}
          </template>
          <template v-slot:item.village="{ item, index }">
            {{ item.village?.vill_name ?? '' }}
          </template>
          <template v-slot:item.district="{ item, index }">
            {{ item.village?.dristric?.dr_name ?? '' }}
          </template>
          <template v-slot:item.province="{ item, index }">
            {{ item.village?.dristric?.province?.pr_name ?? '' }}
          </template>
          <template v-slot:item.acction="{ item }">
            <v-icon small color="primary" @click="openEditUser(item)"
              >mdi-pencil</v-icon
            >
            <v-icon small color="red" class="pl-2" @click="deleteUser(item)"
              >mdi-delete</v-icon
            >
          </template>
        </v-data-table>
      </v-card>
    </template>
  </div>
</template>

<script>
import moment from 'moment'
import Swal from 'sweetalert2'
export default {
  data: () => ({
    headers: [
      { text: 'ຮຸບພາບ', value: 'image' },
      { text: 'ຊື່', value: 'name' },
      { text: 'ນາມສະກຸນ', value: 'surename' },
      { text: 'ວັນ / ເດືອນ / ປີເກີດ', value: 'date' },
      { text: 'ທີ່ຢູ່ປະຈຸບັນ', value: 'village' },
      { text: 'ເມືອງປະຈຸບັນ', value: 'district' },
      { text: 'ແຂວງປະຈຸບັນ', value: 'province' },
      { text: 'ອາຊີບ', value: 'occupation' },
      { text: 'ຕຳແຫນ່ງ ', value: 'position' },
      { text: 'Acctions ', value: 'acction' },
    ],
    user_data: [],
  }),
  methods: {
    async getUserData() {
      try {
        const res = await this.$axios.get('/users/get_users_data')
        console.log(res.data.items)
        if (res.status == 200) {
          this.user_data = res.data.items
        }
      } catch (e) {
        console.error(e)
      }
    },

    getImage(image) {
      if (image === null || image === 'N/A') {
        return 'https://img.freepik.com/free-vector/oops-404-error-with-broken-robot-concept-illustration_114360-5529.jpg?w=1380&t=st=1691852785~exp=1691853385~hmac=93870adb11e61ce34c67a5563628cee8179cf98e2e25739e189cda39293a80ee'
      }
      return `http://localhost:4000${image}`
    },

    formatDate(date) {
      if (date) {
        return moment(date).format('DD/MM/YYYY')
      }
      return date
    },

    openEditUser(item) {
      this.$router.push({
        path: '/employee/edit',
        query: {
          id: item.id,
        },
      })
    },

    async deleteUser(item) {
      try {
        const id = item.id
        if (id) {
          Swal.fire({
            title: `ເຈົ້າຕ້ອງການລຶບ ${item.name} ${item.surename} ບໍ່? `,
            icon: 'warning',
            showCancelButton: true,
            confirmButtonColor: '#3085d6',
            cancelButtonColor: '#d33',
            confirmButtonText: 'ລຶບເລີຍ!',
            cancelButtonText: 'ຍົກເລີກ!',
          }).then(async (result) => {
            if (result.isConfirmed) {
              const res = await this.$axios.delete(`/users/delete/${id}`)
              if (res.status == 200) {
                Swal.fire({ icon: 'success', title: 'ລົບຂໍ້ມູນສຳເລັດແລ້ວ' })
                await this.getUserData()
              }
            }
          })
        }
      } catch (e) {
        console.error(e)
      }
    },
  },
  mounted() {
    this.getUserData()
  },
}
</script>
