<script setup>
import { ref, defineEmits } from 'vue'


const firstName = ref('')
const lastName = ref('')
const email = ref('')
const error = ref(null)

const emit = defineEmits()

const clearForm = () => {
    firstName.value = ''
    lastName.value = ''
    email.value = ''
    error.value = null
}

const validateForm = () => {
    if (!firstName.value.trim() || !lastName.value.trim()) {
        error.value = 'Name is required'
        return false
    }

    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value)) {
        error.value = 'Invalid email address'
        return false
    }

    return true;
}

const putUserOnList = () => {
    emit('add', {
        first_name: firstName.value,
        last_name: lastName.value,
        email: email.value
    })
}

const addUser = async () => {
    if (validateForm()) {
        try {
            const response = await fetch('https://reqres.in/api/users', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify({
                    firstName: firstName.value,
                    lastName: lastName.value,
                    email: email.value,
                }),
            })

            if (response.ok) {
                putUserOnList()
                clearForm()
            }

        } catch (error) {
            console.error('Error adding user:', error)
        }
    }
};
</script>

<template>
    <div class="addUserContainer">
        <h2>Add New User</h2>
        <form @submit.prevent="addUser">
            <div class="formGroup">
                <label for="name">First Name:</label>
                <input id="name" v-model="firstName" placeholder="First Name" />
            </div>
            <div class="formGroup">
                <label for="name">Last Name:</label>
                <input id="name" v-model="lastName" placeholder="Last Name" />
            </div>
            <div class="formGroup">
                <label for="email">Email:</label>
                <input id="email" v-model="email" placeholder="First Name" />
            </div>
            <div class="error" v-if="error">{{ error }}</div>
            <button class="button addUserSubmit" type="submit">Add User</button>
        </form>
    </div>
</template>

<style scoped lang="scss">
@import url('../styles/button.scss');

.addUserContainer {
    margin: 50px auto;

    h2 {
        text-align: center;
        margin-bottom: 20px;
    }

    form {
        display: flex;
        flex-direction: column;
        display: flex;
        align-items: center;

        .formGroup {
            margin-bottom: 15px;
            width: 400px;
            display: flex;

            label {
                font-weight: bold;
                margin-bottom: 5px;
                width: 23%;
                display: flex;
                align-items: center;
            }

            input {
                padding: 8px;
                font-size: 16px;
                margin-left: 10px;
            }
        }

        .error {
            color: red;
            margin-bottom: 10px;
        }

        .addUserSubmit {
            width: 100px;
            height: 40px;
        }
    }
}
</style>
