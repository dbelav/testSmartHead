<script setup>
import { ref, onMounted, computed } from 'vue'
import UserDetailsModal from './UserDetailsModal.vue'
import UserAddForm from './UserAddForm.vue'


const search = ref('')
const users = ref([])
const selectedUser = ref(null)

const fetchUsers = async () => {
    try {
        const response = await fetch('https://reqres.in/api/users')
        const data = await response.json()
        users.value = data.data
    } catch (error) {
        console.error('Error fetching users:', error)
    }
}

const showUserDetails = (user) => {
    selectedUser.value = user

}

const editUser = (updatedUser) => {
    const index = users.value.findIndex(user => user.id === updatedUser.id)

    if(index !== -1){
        const updatedUsers = [...users.value]
        updatedUsers.splice(index, 1, updatedUser)
        users.value = updatedUsers
        selectedUser.value = updatedUser
    }
}

const closeDetailsModal = () => {
    selectedUser.value = null
}

const deleteUser = async (userId) => {
    try {
        const data = await fetch(`https://reqres.in/api/users/${userId}`, {
            method: 'DELETE',
        })

        const newUsers = users.value.filter(user => user.id !== userId)
        users.value = newUsers

    } catch (error) {
        console.error('Error deleting user:', error)
    }
}

const addNewUser = (data) => {
    data.id = users.value.length + 1
    users.value = [...users.value, data]
}

const filteredUsers = computed(() => {
    return users.value.filter(user => {
        const fullName = `${user.first_name} ${user.last_name}`.toLowerCase()
        return fullName.includes(search.value.toLowerCase())
    })
})

onMounted(() => {
    fetchUsers()
})
</script>

<template>
    <div class="userListContainer">
        <input class="userListSearch" v-model="search" placeholder="Search users" />
        <ul class="userList">
            <li class="userListItem" v-for="user in filteredUsers" :key="user.id">
                <div class="userListItemInfo">
                    <img class="userListItemImage" :src="user.avatar" />
                    <span class="userListItemName">{{ user.first_name + ' ' + user.last_name }}</span>
                    <span class="userListItemEmail">{{ user.email }}</span>
                </div>

                <button class="userListItemDetails button" @click="showUserDetails(user)">Details</button>
                <button class="userListItemDelete" @click="deleteUser(user.id)"></button>
            </li>
        </ul>
        <UserDetailsModal 
        v-if="selectedUser" 
        :selectedUser="selectedUser" 
        @close="closeDetailsModal" 
        @edit="editUser"
        />
        <UserAddForm @add="addNewUser" />
    </div>
</template>

<style scoped lang="scss">
@import url('../styles/button.scss');

.userListContainer {
    width: 800px;

    .userListSearch {
        height: 45px;
        width: 200px;
        font-size: 18px;
        padding-left: 10px;
    }

    .userList {
        list-style: none;
        display: flex;
        flex-direction: column;
        padding: 0;

        .userListItem {
            display: flex;
            margin-bottom: 20px;
            border: 2px #f0f0f082 solid;
            align-items: center;
            border-radius: 8px;
            position: relative;
            height: 70px;

            .userListItemInfo {
                width: 80%;
                height: 100%;
                display: flex;
                align-items: center;

                .userListItemName {
                    width: 50%;
                    display: flex;
                    align-items: center;
                }

                .userListItemEmail {
                    width: 50%;
                    display: flex;
                    align-items: center;
                }

                .userListItemImage {
                    height: 90%;
                    margin: 0 20px;
                    border-radius: 50%;
                }
            }

            .userListItemDetails {
                width: 90px;
                height: 40px;
            }

            .userListItemDelete {
                position: absolute;
                top: 30px;
                right: 20px;
                width: 24px;
                height: 24px;
                opacity: 0.8;
                cursor: pointer;
                transition: opacity ease 0.5s;
                top: 2px;
                right: 2px;
                z-index: 2;
                background-color: transparent;
                border: none;
                padding: 0;
                border-radius: 50%;

                &:hover {
                    opacity: 1;
                }
            }

            .userListItemDelete::before,
            .userListItemDelete::after {
                content: '';
                position: absolute;
                top: 10px;
                display: block;
                width: 24px;
                height: 3px;
                background: #b8b8b8;
            }

            .userListItemDelete::before {
                transform: rotate(45deg);
            }

            .userListItemDelete::after {
                transform: rotate(-45deg);
            }
        }
    }
}
</style>
  