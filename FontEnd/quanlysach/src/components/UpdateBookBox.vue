<template>
    <div class="overlay">
        <div class="form-add-book">
            <v-form>
                <v-text-field v-model="nameBook" 
                  required>
                </v-text-field>

                <textarea style="width: 100%; height:100px;" v-model="desBook"
                    required>
                </textarea>


                <v-file-input 
                class="input-img-book"
                v-model="textAttachBook"
                chips
                :label="linkAttachBook" accept="image/png, image/jpeg"
                @change="changeImage"
                
                ></v-file-input>
        </v-form>

            
            <div class="footer-Update">
                <v-btn
                    variant="flat"
                    color="error"
                    @click="this.$emit('close-box')"
                    >
                    Hủy
                </v-btn>

                <v-btn
                    variant="flat"
                    color="info"
                    class="btn-Update"
                    @click="updateBook"
                    >
                    Cập nhật
                </v-btn>       
            </div>

        </div>
    </div>
</template>

<script >
import axios from 'axios'

export default {
    name: 'UpdateBookBox',
    props: ['idBookSelect'],
    data() {
        return{
            arrBook: [],
            idBook: this.idBookSelect,
            nameBook:'',
            desBook:'',
            linkAttachBook: '',
            textAttachBook: '',
            newImageBook: '',
        }
    },
    
    mounted(){
        this.idBook = this.idBookSelect,
        this.getAPISach()
    },

    methods:{
        changeImage(){
            var fileCheck = Object.assign({}, this.textAttachBook) 

            //CLEAR OLD NAME
            this.linkAttachBook = ''
            this.newImageBook = fileCheck[0].name
        },
        

        getAPISach(){
            axios.get(`/SelectSachByID/${this.idBook}`)
            .then((response) => {
                this.arrBook.push(...response.data);
                this.nameBook = response.data[0].TenSach
                this.desBook = response.data[0].MoTa
                this.linkAttachBook = response.data[0].Image
            })
        },

        async updateBook(){

            let result = await axios.put('/UpdateSach',{
                    "TenSach": `${this.nameBook}`,
                    "MoTa": `${this.desBook}`,
                    "MaSach": this.idBook,
                    "Image": `${this.newImageBook}`
                })
            
            if (result.status == 200 && result.data.length > 0) {
                this.getAPISach();
                location.reload()
            }
            else {
                this.snackbar = true
                this.snackbarText = 'Xóa không thành công';
            }

        }
    }
}
</script>

<style scoped>
.overlay{
    display: flex;
    align-items: center;
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0,0,0, 0.4);
    z-index: 3;
}


.form-add-book{
    width: 800px;
    margin: 0 auto;
    height: auto;
    background-color: white;
    box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
    border-radius: 12px;
    padding: 20px;
}


.footer-Update{
    display: flex;
    align-items: center;
    justify-content: flex-end;
}


.btn-Update{
    margin-left: 10px;
}
</style>