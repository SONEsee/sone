<template>
  <div>
    <v-form ref="form_create_user" @submit.prevent="create_user()">
      <v-col cols="12" class="ma-0 pa-0 d-flex flex-wrap">
        <v-col cols="12" class="flex-wrap">
          <h3>ເພີ່ມຂໍ້ມູນບຸກຄົນ</h3>
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
                :src="file == null ? '' : return_imageobject()"
              ></v-img>
            </v-card>
          </v-col>
          <v-col cols="2" class="ma-0 pa-0 px-2">
            <v-menu
              ref="menu"
              v-model="menu"
              :close-on-content-click="false"
              :return-value.sync="date"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  solo
                  filled
                  dense
                  v-model="date"
                  label="ວັນ/ເດືອນ/ປີເກີດ"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                  required
                  :rules="[(v) => !!v || 'Date of birth is require']"
                ></v-text-field>
              </template>
              <v-date-picker v-model="date" no-title scrollable>
                <v-spacer></v-spacer>
                <v-btn text color="primary" @click="menu = false">
                  Cancel
                </v-btn>
                <v-btn text color="primary" @click="$refs.menu.save(date)">
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
              v-model="name"
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
              v-model="surname"
            >
            </v-text-field>
          </v-col>
        </v-col>
        <v-col cols="12" class="ma-0 pa-0 d-flex flex-wrap">
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-select
              solo
              :items="provinces"
              item-text="pr_name"
              item-value="pr_id"
              dense
              fiiled
              label="ແຂວງຢູ່ປະຈຸບັນ"
              required
              v-model="province_id"
              @change="handleProvinceChange"
              :rules="[(v) => !!v || 'Province is require']"
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
              :items="districts"
              item-value="dr_id"
              :loading="isLoading"
              item-text="dr_name"
              v-model="district_id"
              :rules="[(v) => !!v || 'Distric is require']"
              @change="handleDistrictChange"
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
              item-text="vill_name"
              item-value="vill_id"
              :items="village"
              :loading="isLoading"
              v-model="village_id"
              :rules="[(v) => !!v || 'Village is require']"
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
              required
              v-model="occupation"
              :rules="[(v) => !!v || 'Occupation is require']"
            >
            </v-text-field>
          </v-col>
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-text-field solo dense fiiled label="ຕຳແໜ່ງ" v-model="position">
            </v-text-field>
          </v-col>
          <v-col cols="4" class="ma-0 pa-0 px-2">
            <v-file-input
              label="ເລືອກຮູບພາບ"
              solo
              dense
              filled
              v-model="file"
              required
              :rules="[(v) => !!v || 'Image is require']"
              accept="image/png, image/jpeg , image/jpg"
            ></v-file-input>
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
import Swal from 'sweetalert2'
export default {
  data: () => ({
    date: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
      .toISOString()
      .substr(0, 10),
    menu: false,
    provinces: [],
    districts: [],
    village: [],
    province_id: null,
    district_id: null,
    village_id: null,
    isLoading: false,
    name: '',
    surname: '',
    occupation: '',
    file: null,
    position: '',
  }),

  methods: {
    return_imageobject() {
      if (this.file !== null) {
        return URL.createObjectURL(this.file)
      } else {
        return ''
      }
    },
    async create_user() {
      try {
        const validate = await this.$refs.form_create_user.validate()
        if (validate) {
          var formData = new FormData()
          if (this.file) {
            formData.append('file', this.file, this.file.name)
          }
          formData.append('name', this.name)
          formData.append('surename', this.surname)
          formData.append('occupation', this.occupation)
          formData.append('position', this.position)
          formData.append('village_id', this.village_id)
          formData.append('date_of_birth', this.date)
          const config = {
            headers: {
              'Content-Type': 'multipart/form-data',
            },
          }
          const res = await this.$axios.post('/users/create', formData, config)
          if (res.status == 200) {
            Swal.fire({
              icon: 'success',
              title: 'ເພີ່ມຂໍ້ມູນບຸກຄົນສຳເລັດແລ້ວ',
            })
          }
        }
      } catch (error) {
        console.error(error)
      } finally {
        this.$refs.form_create_user.reset()
        this.province_id = null
        this.district_id = null
        this.village_id = null
        return this.$router.push({
          path: '/employee',
        })
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

        this.district_id = null
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

        this.village_id = null
      } catch (error) {
        console.error(e)
      } finally {
        this.loading = false
      }
    },
  },

  mounted() {
    this.getProvince()
  },
}
</script>
