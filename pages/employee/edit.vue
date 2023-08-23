<template>
  <div v-if="user_data !== null">
    <v-form ref="form_edit_user" @submit.prevent="edit_user()">
      <v-col cols="12" class="ma-0 pa-0 d-flex flex-wrap">
        <v-col cols="12" class="flex-wrap">
          <h3>ແກ້ໄຂຂໍ້ມູນບຸກຄົນ</h3>
        </v-col>
        <v-col cols="12" class="ma-0 pa-0 d-flex flex-wrap align-end">
          <v-col cols="2" class="ma-0 pa-0 px-2">
            <v-card
              width="150"
              height="170"
              class="ma-1"
              elevation="0"
              color="grey lighten-4"
              tile
            >
              <v-img
                width="100%"
                height="100%"
                :src="
                  file == null
                    ? getImage(user_data.image)
                    : return_imageobject()
                "
              ></v-img>
            </v-card>
          </v-col>
          <v-col cols="2" class="ma-0 pa-0 px-2">
            <v-menu
              ref="menu"
              v-model="menu"
              :close-on-content-click="false"
              :return-value.sync="user_data.date_of_birth"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  solo
                  filled
                  dense
                  v-model="formatDate"
                  label="ວັນ/ເດືອນ/ປີເກີດ"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                  required
                  :rules="[(v) => !!v || 'Date of birth is require']"
                ></v-text-field>
              </template>
              <v-date-picker
                v-model="user_data.date_of_birth"
                no-title
                scrollable
              >
                <v-spacer></v-spacer>
                <v-btn text color="primary" @click="menu = false">
                  Cancel
                </v-btn>
                <v-btn
                  text
                  color="primary"
                  @click="$refs.menu.save(user_data.date_of_birth)"
                >
                  OK
                </v-btn>
              </v-date-picker>
            </v-menu>
          </v-col>
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-text-field
              solo
              label="ຊື່"
              filled
              dense
              required
              :rules="[(v) => !!v || 'Name is require']"
              v-model="user_data.name"
            >
            </v-text-field>
          </v-col>
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-text-field
              solo
              label="ນາມສະກຸນ"
              filled
              dense
              required
              :rules="[(v) => !!v || 'Surname is require']"
              v-model="user_data.surename"
            >
            </v-text-field>
          </v-col>
        </v-col>
        <v-col cols="12" class="ma-0 pa-0 d-flex flex-wrap">
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-select
              solo
              dense
              fiiled
              label="ແຂວງຢູ່ປະຈຸບັນ"
              required
              :items="provinces"
              item-text="pr_name"
              item-value="pr_id"
              v-model="user_data.village.dristric.province.pr_id"
              :rules="[(v) => !!v || 'Province is require']"
              @change="handleProvinceChange"
            >
            </v-select>
          </v-col>
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-select
              solo
              dense
              fiiled
              label="ເມືອງຢູ່ປະຈຸບັນ"
              required
              :rules="[(v) => !!v || 'Distric is require']"
              :items="districts"
              item-text="dr_name"
              item-value="dr_id"
              v-model="user_data.village.dristric.dr_id"
              @change="handleDistrictChange"
              :loading="isLoading"
            >
            </v-select>
          </v-col>
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-select
              solo
              dense
              fiiled
              label="ບ້ານຢູ່ປະຈຸບັນ"
              required
              :rules="[(v) => !!v || 'Village is require']"
              :items="village"
              item-text="vill_name"
              item-value="vill_id"
              v-model="user_data.village.vill_id"
              :loading="isLoading"
            >
            </v-select>
          </v-col>
        </v-col>
        <v-col cols="12" class="ma-0 pa-0 d-flex flex-wrap">
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-text-field
              solo
              dense
              fiiled
              label="ອາຊີບ"
              v-model="user_data.occupation"
              required
              :rules="[(v) => !!v || 'Occupation is require']"
            >
            </v-text-field>
          </v-col>
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-text-field
              solo
              dense
              fiiled
              label="ຕຳແໜ່ງ"
              v-model="user_data.position"
            >
            </v-text-field>
          </v-col>
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <template>
              <v-file-input
                label="ແກ້ໄຂຮູບພາບ"
                solo
                dense
                filled
                accept="image/png, image/jpeg , image/jpg"
                v-model="file"
              ></v-file-input>
            </template>
          </v-col>
        </v-col>
        <v-col cols="12" class="d-flex flex-wrap">
          <v-btn width="100%" elevation="0" color="primary" dark type="submit">
            <h3>ບັນທຶກ</h3>
          </v-btn>
        </v-col>
      </v-col>
    </v-form>
  </div>
</template>
<script>
import moment from 'moment'
import Swal from 'sweetalert2'
export default {
  data: () => ({
    date: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
      .toISOString()
      .substr(0, 10),
    menu: false,
    isLoading: false,
    user_data: null,
    provinces: [],
    districts: [],
    village: [],
    file: null,
  }),

  computed: {
    formatDate() {
      if (this.user_data) {
        return moment(this.user_data.date_of_birth).format('DD/MM/YYYY')
      } else {
        return ''
      }
    },
  },
  methods: {
    getImage(image) {
      if (image === null || image === 'N/A') {
        return 'https://img.freepik.com/free-vector/oops-404-error-with-broken-robot-concept-illustration_114360-5529.jpg?w=1380&t=st=1691852785~exp=1691853385~hmac=93870adb11e61ce34c67a5563628cee8179cf98e2e25739e189cda39293a80ee'
      }
      return `http://localhost:4000${image}`
    },

    return_imageobject() {
      if (this.file !== null) {
        return URL.createObjectURL(this.file)
      } else {
        return ''
      }
    },

    async getUserDataById() {
      try {
        const id = this.$route.query.id
        if (id == null || undefined) {
          return this.$router.push({ path: '/' })
        }
        const res = await this.$axios.get(`/users/get_user_data_by_id/${id}`)
        if (res.status == 200) {
          this.user_data = res.data.items
          const village = res.data.items.village_id
          const districtId = res.data.items.village?.dristric?.dr_id
          const provinceId = res.data.items.village?.dristric?.province?.pr_id
          this.getProvince()
          this.getDistricts(provinceId)
          this.getVillage(districtId)
        }
      } catch (e) {
        console.error(e)
      }
    },

    async getProvince() {
      try {
        const res = await this.$axios.get('/province')
        if (res.status === 200) {
          this.provinces = res.data.items
        }
      } catch (e) {
        console.error(e)
      }
    },

    async getDistricts(value) {
      try {
        if (value) {
          const res = await this.$axios.get(`/district/${value}`)
          if (res.status === 200) {
            const { items } = res.data
            this.districts = items
          }
        }
      } catch (error) {
        console.error(error)
      }
    },

    async getVillage(value) {
      try {
        if (value) {
          const res = await this.$axios.get(`/village/${value}`)
          if (res.status === 200) {
            const { items } = res.data
            this.village = items
          }
        }
      } catch (error) {
        console.error(e)
      }
    },

    async handleProvinceChange(value) {
      try {
        this.isLoading = true
        if (value) {
          const res = await this.$axios.get(`/district/${value}`)
          if (res.status === 200) {
            const { items } = res.data
            this.districts = items
          }
        }

        this.user_data.village.vill_id = null
        this.user_data.village.dristric.dr_id = null
      } catch (error) {
        console.error(error)
      } finally {
        this.isLoading = false
      }
    },

    async handleDistrictChange(value) {
      try {
        this.loading = true
        if (value) {
          const res = await this.$axios.get(`/village/${value}`)
          if (res.status === 200) {
            const { items } = res.data
            this.village = items
          }
        }
      } catch (error) {
        console.error(e)
      } finally {
        this.loading = false
      }
    },

    async edit_user() {
      try {
        const validate = await this.$refs.form_edit_user.validate()
        if (validate) {
          var formData = new FormData()
          if (this.file) {
            formData.append('file', this.file, this.file.name)
          }
          formData.append('name', this.user_data.name)
          formData.append('surename', this.user_data.surename)
          formData.append('occupation', this.user_data.occupation)
          formData.append('position', this.user_data.position)
          formData.append('village_id', this.user_data.village.vill_id)
          formData.append('date_of_birth', this.user_data.date_of_birth)
          const config = {
            headers: {
              'Content-Type': 'multipart/form-data',
            },
          }
          const id = this.user_data.id
          const res = await this.$axios.put(
            `users/update/${id}`,
            formData,
            config
          )
          if (res.status == 200) {
            Swal.fire({
              icon: 'success',
              title: 'ອັບເດດຂໍ້ມູນສຳເລັດແລ້ວ',
            })
            await this.getUserDataById()
            this.file = null
          }
        }
      } catch (e) {
        console.error(e)
      }
    },
  },
  mounted() {
    this.getUserDataById()
  },
}
</script>
