<template>
    <v-card width="500" class="mx-auto mt-9" ref="form">
        <v-card-title>Đăng kí tài khoản</v-card-title>

        <v-form ref="form" v-model="vaild">
            <v-card-text>
                <v-text-field v-model="username" :rules="usernameRules"
                                label="Username" prepend-icon="mdi-account-circle"
                                required>
                </v-text-field>

                <v-text-field v-model="password" :rules="passwordRules"
                                label="Password"
                                :type="showPassword ? 'text' : 'password' " prepend-icon="mdi-lock"
                                :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off' "
                                v-on:keyup.enter="signUp"
                                @click:append="showPassword = !showPassword" required>
                </v-text-field>
            </v-card-text>

            <v-divider></v-divider>

            <v-card-actions class="justify-center">
                
                <v-btn :disabled="!valid" 
                    color="info" 
                    @click="backToHomePage" 
                    >
                    Quay lại
                </v-btn>

                <v-btn color="success" v-on:click="registerAccount"> Đăng kí </v-btn>
            </v-card-actions>

            <v-alert 
            v-if="errorLoginToast"
            type="error" 
            :value="alert" 
            :timeout="2000"> 
            Tài khoản hoặc mật khẩu không chính xác </v-alert>


            <v-snackbar
                v-model="snackbar"
                :timeout="timeout"
                >
                {{snackbarText}}

            <template v-slot:actions>
                <v-btn
                color="blue"
                variant="text"
                @click="snackbar = false"
                >
                Close
                </v-btn>
            </template>
            </v-snackbar>


        </v-form>

    </v-card>
    
</template>
    
    <script>
import router from '@/router';
import axios from 'axios'

    export default {
      name: 'Register',
      data() {
        return{
        snackbar: false,
        snackbarText: '',
        timeout: 2000,
        errorLoginToast: false,
        alert: false,
        showPassword: false,
        valid: true,
        password: '',
        passwordRules: [
              v => !!v || 'You need to input password',
              v => (v && v.length > 0) || 'Password must have character',
          ],
          username: '',
          usernameRules: [
              v => !!v || 'You need to input username',
              v => (v && v.length > 0) || 'Username must have character',
          ],
        }
      },

      mounted(){
        let user = window.localStorage.getItem("user-info");
        if (user) {
            router.push('/home')
        }
    },  
    
      methods: {
        async registerAccount() {
            if (this.username != '' && this.password != '') {

                let result = await axios.post('/AddNewUser',{
                    "UserName": `${this.username}`,
                    "UserPass": `${this.password}`,
                })
    
                if (result.status == 200 && result.data.length > 0) {

                    this.snackbar = true
                    this.snackbarText = 'Thêm tài khoản thành công'

                    setTimeout(() => {
                        router.push("/")
                    },1500)    
                }
                else {
                    this.snackbar = true
                    this.snackbarText = 'Thêm tài khoản không thành công'
                }
            }
            else {
                this.snackbar = true
                this.snackbarText = 'Chưa thỏa điều kiện đăng kí'
            }
        },

        backToHomePage() {
            router.push("/")
        }
      },
    
    }
    </script>
    