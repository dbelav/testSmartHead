<script setup>
import { ref, defineProps, defineEmits } from 'vue'


const { selectedUser } = defineProps(['selectedUser'])
const emit = defineEmits()

const isEdit = ref(false)
const newNumber = ref('')
const newAddress = ref('')

const closeModal = () => {
    emit('close')
}

const editUser = () => {
    if (isEdit.value) {
        console.log(isEdit)
        emit('edit', {
            ...selectedUser,
            phoneNumber: newNumber.value,
            address: newAddress.value
        })
    }
    isEdit.value = !isEdit.value
}

</script>

<template>
    <div class="modal" v-if="selectedUser">
        <div class="modalContent">
            <h2 class="modalTitle">User Details</h2>
            <div class="modalItem">
                <strong class="modalItemTitle">Name:</strong> {{ selectedUser.first_name }} {{ selectedUser.last_name }}
            </div>
            <div class="modalItem">
                <strong class="modalItemTitle">Email:</strong> {{ selectedUser.email }}
            </div>
            <div class="modalItem">
                <strong class="modalItemTitle">Phone:</strong>
                <span v-if="!isEdit">{{ selectedUser.phoneNumber || ' N/A' }}</span>
                <input class="modalItemInput" v-else v-model="newNumber" />
            </div>
            <div class="modalItem">
                <strong class="modalItemTitle">Address:</strong>
                <span v-if="!isEdit">{{ selectedUser.address || ' N/A' }}</span>
                <input class="modalItemInput" v-else v-model="newAddress" />
            </div>
            <div class="modalButtonsContainer">
                <button class="button modalEditButton" @click="editUser">{{ isEdit ? 'Save' : 'Edit' }}</button>
                <button class="button modalCloseButton" @click="closeModal">Close</button>
            </div>
        </div>
    </div>
</template>
  
<style scoped lang="scss">
@import url('../styles/button.scss');

.modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 4;

    .modalContent {
        width: 400px;
        height: 400px;
        background-color: rgb(0, 0, 0);
        padding: 20px 25px;
        border-radius: 8px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
        display: flex;
        flex-direction: column;

        .modalTitle{
            margin: 0;
        }

        .modalItem{            
            height: 35px;
            margin: 10px 0;
            display: flex;

            .modalItemTitle{
                margin-right: 8px;
            }
            .modalItemInput{
                height: 100%;
            }
        }

        .modalButtonsContainer {
            display: flex;
            flex-direction: column;
            align-items: center;

            .modalCloseButton{
                width: 100px;
                height: 50px;
                margin: 15px 0;
            }
            .modalEditButton {
                width: 90px;
                height: 40px;
                margin: 15px 0;
            }
        }
    }
}
</style>
  