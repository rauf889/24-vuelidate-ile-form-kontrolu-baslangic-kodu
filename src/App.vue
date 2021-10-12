<template>
  <div class="container">
    <h3 class="text-center mt-3">Vuelidate ile Form Kontrolü</h3>
    <div class="d-flex justify-content-center align-content-center flex-row">
      <div class="card p-4 mt-3 mr-4  shadow">

        <!-- <div class="card p-4 m-3 shadow" style="width: 400px">
          <p>{{$v.password}}</p>
        </div> -->
        <form style="width: 350px" @submit.prevent="onSubmit">
          <div class="form-group">
            <label>E-posta Adresiniz</label>
            <input
              @blur="$v.email.$touch()"
              v-model="email"
              type="email" 
              class="form-control" 
              :class="{'is-invalid': $v.email.$error}"
              placeholder="E-posta adresini giriniz">
            <small v-if="!$v.email.required" class="form-text text-danger">Bu alan zorunludur...</small>
            <small v-if="!$v.email.email" class="form-text text-danger">Lutfen gecerli bi adress giriniz...</small>
          </div>
          <div class="form-group">
            <label>Şifre</label>
            <input v-model="$v.password.$model" type="password" class="form-control" placeholder="Şifrenizi giriniz">
            <small v-if="!$v.password.required" class="form-text text-danger">Bu alan zorunludur...</small>
            <small v-if="!$v.password.numeric" class="form-text text-danger">Lutfen sifreniz rakamlardan olussun...</small>
            <small v-if="!$v.password.minLength" class="form-text text-danger">Lutfen sifreniz en az {{$v.password.$params.minLength.min}} kar olussun...</small>
            <small v-if="!$v.password.maxLength" class="form-text text-danger">Lutfen sifreniz en fazla {{$v.password.$params.maxLength.max}} kar olussun...</small>
          </div>   
          <div class="form-group">
            <label>Şifre Tekrar</label>
            <input v-model="$v.repassword.$model" type="password" class="form-control" placeholder="Şifrenizi tekrar giriniz">
            <small v-if="!$v.repassword.numeric" class="form-text text-danger">Lutfen sifreniz rakamlardan olussun...</small>
            <small v-if="!$v.repassword.required" class="form-text text-danger">Bu alan zorunludur...</small>
            <small v-if="!$v.repassword.minLength" class="form-text text-danger">Lutfen sifreniz en az {{$v.repassword.$params.minLength.min}} kar olussun...</small>
            <small v-if="!$v.repassword.maxLength" class="form-text text-danger">Lutfen sifreniz en fazla {{$v.repassword.$params.maxLength.max}} kar olussun...</small>
            <small v-if="!$v.repassword.sameAs" class="form-text text-danger">Girdiginiz sifreler birbirleriyle uyusmuyor...</small>
          </div>

          <div class="form-group">
            <label>Yasiniz</label>
            <input v-model="$v.age.$model" type="text" class="form-control" placeholder="Yasinizi giriniz">
            <small v-if="!$v.age.required" class="form-text text-danger">Bu alan zorunludur...</small>
            <small v-if="!$v.age.numeric" class="form-text text-danger">Lutfen rakamlardan olussun...</small>
            <small v-if="!$v.age.between" class="form-text text-danger">Sistem kayiti icin
              {{$v.age.$params.between.min}} ile {{$v.age.$params.between.max}} arasinda olmali</small>
          </div>

          <div class="form-group">
            <label>Kayıt olmak istediğiniz kategori</label>
            <select v-model="$v.selectedCategory.$model" class="form-control">
              <option v-for="category in categories" :value="category" :key="category.id">{{ category }}</option>
            </select>
            <small v-if="!$v.selectedCategory.checked" class="form-text text-danger">Sadece Yazilim kat ait kait olusturabilirsiniz...</small>


          </div>

          <a @click="newHobby" class="text-white btn btn-secondary rounded-0 btn-sm">İlgi Alanı Ekle</a>
            <small v-if="!$v.hobbies.required" class="form-text text-danger">Bu alan zorunludur...</small>
            <small v-if="!$v.hobbies.minLength" class="form-text text-danger">En az {{$v.hobbies.$params.minLength.min}} tane olmalidir...</small>
            
          <ul class="list-group mt-3 mb-3 border-0">
            <li v-for="(hobby,index) in hobbies" :key="hobby.id" class="list-group-item d-flex pl-2">
              <input 
              type="text" 
              class="form-control mr-2"
              :class="{ 'is-invalid' : $v.hobbies.$each[index].$error}"
              @blur="$v.hobbies.$each[index].value.$touch()"
              v-model="hobby.value">
              <button class="btn btn-sm btn-danger rounded-0" @click="hobbies.splice(index, 1)">Sil</button>
            </li>
          </ul>
         
          <button class="btn btn-outline-secondary rounded-0" :disabled="$v.$invalid">Kaydet</button>
        </form>
      </div>
      <div class="card p-4 m-3 shadow" style="width: 400px">
        <p>{{$v.repassword}}</p>
      </div>
    </div>
  </div>
</template>
<script>
import {required, email, numeric, minLength, maxLength, sameAs, between} from "vuelidate/lib/validators"

  export default {
    data() {
      //bir alani kontrol edebilmek icin, data da tanimli olmali
      return {
        email : null,
        password : null,
        repassword : null,
        selectedCategory: "Yazılım",
        age: null,
        categories : ["Yazılım", "Donanım", "Cloud", "Sunucular", "Unix", "Linux", "Mac OS", "Windows"],
        hobbies: []
      }
    },
    validations:{
      email: {
        required,
        email,
        // uniq : value => {
        //   return value !== 'ryagubov@gmail.com'
        // }
        uniq : value => {
          return new Promise((resolve, reject)=>{
            setTimeout(()=>{
              resolve(value !== 'ryagubov@gmail.com')
            }, 1000)
          })
          // return axios.get("url")
          // .then(response=>{
          //   return false
          // })
          // return value !== 'ryagubov@gmail.com'
        }
      },
      password:{
        required,
        numeric,
        minLength: minLength(6),
        maxLength: maxLength(8)
      },
      repassword:{
        required,
        numeric,
        minLength: minLength(6),
        maxLength: maxLength(8),
        // sameAs: sameAs('password')
        sameAs: sameAs( vm=> {
          return vm.password + "87"
          //passwd: 123456, repasswd:12345687
        })
      },
      age:{
        required,
        numeric,
        between: between(18, 60)
      },
      selectedCategory:{
        checked(val, vm){
          return vm.selectedCategory === "Yazılım" ? true: false
        }
      },
      hobbies: {
        required,
        minLength : minLength(2),
        $each:{
          value: {
            required,
            minLength: minLength(6)
          }
        }
      }
    },
    methods: {
      onSubmit(){
        let form = {
          email : this.email,
          password : this.password,
          category : this.category,
          hobbies : this.hobbies,
        }
        console.log(form)
      },
      newHobby(){
        let hobby = {
          id: Math.random() * Math.random() * 1000,
          value: ''
        }
        this.hobbies.push(hobby)
      }
    }
  }
</script>
