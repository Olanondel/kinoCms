<template>
  <Preloader v-if="!init"/>
  <div v-else>
    <h2 v-if="!this.allUsers" style="text-align: center; margin: 20px 0">Список пользователей пуст</h2>

    <div class="card general__margin" v-else>
      <div class="card-header">
        <h3 class="card-title">Пользователи</h3>
        <div class="input-group">
          <div class="input-group-prepend">
            <span class="input-group-text"><i class="fas fa-search"></i></span>
          </div>
          <input type="search" class="form-control" placeholder="Search" v-model="searchText">
        </div>
      </div>
      <!-- /.card-header -->
      <div class="card-body">


        <table id="example2" class="table table-bordered table-hover dataTable dtr-inline" role="grid"
               aria-describedby="example2_info">
          <UsersTableHeader :checkMode="checkMode"/>
          <tbody>
          <UsersTableRow
              v-for="(user, index) in filteredUsers"
              :key="user.id"
              :data="user"
              @remove="removeUser"
              :index="index"
              :checkMode="checkMode"
              @addTo="addToMailing"
              :contactType="contactType"
          />
          </tbody>
        </table>

        <div class="row" v-if="false">
          <div class="col-sm-12 col-md-5">
            <div class="dataTables_info" id="example2_info" role="status" aria-live="polite">Showing 1 to 10 of 57
              entries
            </div>
          </div>
          <div class="col-sm-12 col-md-7">
            <div class="dataTables_paginate paging_simple_numbers" id="example2_paginate">
              <ul class="pagination">
                <li class="paginate_button page-item previous disabled" id="example2_previous"><a href="#"
                                                                                                  aria-controls="example2"
                                                                                                  data-dt-idx="0"
                                                                                                  tabindex="0"
                                                                                                  class="page-link">Previous</a>
                </li>
                <li class="paginate_button page-item active"><a href="#" aria-controls="example2" data-dt-idx="1"
                                                                tabindex="0" class="page-link">1</a></li>
                <li class="paginate_button page-item "><a href="#" aria-controls="example2" data-dt-idx="2"
                                                          tabindex="0" class="page-link">2</a></li>
                <li class="paginate_button page-item "><a href="#" aria-controls="example2" data-dt-idx="3"
                                                          tabindex="0" class="page-link">3</a></li>
                <li class="paginate_button page-item "><a href="#" aria-controls="example2" data-dt-idx="4"
                                                          tabindex="0" class="page-link">4</a></li>
                <li class="paginate_button page-item "><a href="#" aria-controls="example2" data-dt-idx="5"
                                                          tabindex="0" class="page-link">5</a></li>
                <li class="paginate_button page-item "><a href="#" aria-controls="example2" data-dt-idx="6"
                                                          tabindex="0" class="page-link">6</a></li>
                <li class="paginate_button page-item next" id="example2_next"><a href="#" aria-controls="example2"
                                                                                 data-dt-idx="7" tabindex="0"
                                                                                 class="page-link">Next</a></li>
              </ul>
            </div>
          </div>
        </div>
      </div>
      <SaveButton @saveEvent="startMailing" text="Отправить" v-if="checkMode"/>
    </div>
  </div>
</template>

<script>
import Preloader from "../../../components/admin/general/Preloader";
import UsersTableHeader from "../../../components/admin/users/UsersTableHeader";
import UsersTableRow from "../../../components/admin/users/UsersTableRow";
import server from '../../../requests/admin/requests'
import SaveButton from "../../../components/admin/general/SaveButton";
import {mapGetters} from 'vuex'

export default {
  components: {SaveButton, UsersTableRow, UsersTableHeader, Preloader},
  name: "users",
  data() {
    return {
      init: true,
      searchText: '',
      mailingList: {
        email: [],
        phone: [],
      }
    }
  },
  computed: {
    ...mapGetters(["allUsers"]),
    filteredUsers() {
      return this.allUsers.filter(el => {
        let arr = Object.values(el)

        return arr.length ? arr.some(this.includes) : []
      })
    },
    checkMode() {
      return this.$route.params.mode || false
    },
    contactType() {
      return this.$route.params.type || ''
    }
  },
  methods: {
    includes(el) {
      if (typeof (el) === "string") {
        if (el.toLowerCase().includes(this.searchText.toLowerCase())) {
          return true
        }
      }
    },
    addToMailing(value, type) {
      if (!this.mailingList[type].includes(value)) {
        this.mailingList[type].push(value)
      }
    },
    async removeUser(id, index) {

      this.users[index].isFetching = true
      await server.removeElement(id, this.users[index].mainImage, this.users[index].images, 'Users')
      this.users.splice(index, 1)
    },
    startMailing() {
      if (this.mailingList[this.contactType] && this.mailingList[this.contactType].length) {
        let message = this.$route.params.messageText

        if (message && message.length) {
          this.mailingList[this.contactType].forEach(el => {
            console.log(
                el, ' => ', message
            )
          })

          alert('success')
          this.$router.push({name: 'mailing'})
        } else {
          alert('Message can not be empty')
        }
      }
    },
  },
  created() {
    this.$store.dispatch('getUsers')
  }
}
</script>

<style scoped>
.table {
  max-width: 100%;
}

.card {
  overflow-x: auto;
}

.card-title {
  margin: 15px;
}

.input-group {
  max-width: 300px;
}
</style>