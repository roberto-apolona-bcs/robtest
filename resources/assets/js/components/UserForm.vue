<template>
    <transition name="fade">
    <div class="row">
        <div class="col-md-6">
            <h2 class="sub-header">Users Form</h2>
        </div>

        <div class="col-md-6 text-right">
            <button class="btn btn-primary" @click="backToList">Back to User List</button>
        </div>

        <div class="col-md-12" v-if="msg!=null">
            <div class="alert" :class="alertClass" role="alert">
                <span class="glyphicon glyphicon-exclamation-sign" aria-hidden="true"></span>
                <span class="sr-only">Error:</span>
                {{ msg }}
            </div>
        </div>

        <div class="col-md-12">
            <form role="form">
                <div class="row">
                    <div class="col-xs-6 col-sm-6 col-md-6">
                        <div class="form-group" :class="{ 'control' : true }">
                            <input v-model="user.firstname" type="text" v-validate="'required|alpha'" name="firstname" :class="{ 'input' : true, 'is_danger' : errors.has('firstname')}" id="firstname" class="form-control input-sm" placeholder="First Name">
                            <span v-show="errors.has('firstname')" class="help is-danger">{{ errors.first('fristname') }}</span>
                        </div>
                    </div>
                    <div class="col-xs-6 col-sm-6 col-md-6">
                        <div class="form-group" :class="{ 'control' : true }">
                            <input v-model="user.lastname" type="text" v-validate="'required|alpha'" name="lastname" :class="{ 'input' : true, 'is_danger' : errors.has('lastname')}" id="lastname" class="form-control input-sm" placeholder="Last Name">
                            <span v-show="errors.has('lastname')" class="help is-danger">{{ errors.first('lastname') }}</span>
                        </div>
                    </div>
                </div>

                <div class="row">
                    <div class="col-xs-6 col-sm-6 col-md-6">
                        <div class="form-group" :class="{ 'control' : true }">
                            <input v-model="user.email" type="text" v-validate="'required|email'" name="email" :class="{ 'input' : true, 'is_danger' : errors.has('email')}" id="email" class="form-control input-sm" placeholder="Email">
                            <span v-show="errors.has('email')" class="help is-danger">{{ errors.first('email') }}</span>
                        </div>
                    </div>
                    <div class="col-xs-6 col-sm-6 col-md-6">
                        <div class="form-group" :class="{ 'control' : true }">
                            <input v-model="user.username" type="text" v-validate="'required|alpha'" name="username" :class="{ 'input' : true, 'is_danger' : errors.has('username')}" id="username" class="form-control input-sm" placeholder="Username">
                            <span v-show="errors.has('username')" class="help is-danger">{{ errors.first('username') }}</span>
                        </div>
                    </div>
                </div>

                <div class="row"  :class="{ 'control' : true }">
                    <div class="col-xs-6 col-sm-6 col-md-6">
                        <div class="form-group">
                            <input v-model="user.password" v-validate="'required'"  type="password" name="password" id="password" class="form-control input-sm" placeholder="Password">
                            <span v-show="errors.has('password')" class="help is-danger">{{ errors.first('password') }}</span>
                        </div>
                    </div>
                    <div class="col-xs-6 col-sm-6 col-md-6">
                        <div class="form-group">
                            <input type="password" v-validate="'required|confirmed:password'" name="password_confirmation" id="password_confirmation" class="form-control input-sm" placeholder="Confirm Password">
                            <span v-show="errors.has('password_confirmation')" class="help is-danger">{{ errors.first('password_confirmation') }}</span>
                        </div>
                    </div>
                </div>

                <hr />

                <div class="row">
                    <div class="col-xs-12 col-sm-12 col-md-12">
                        <div class="form-group" :class="{ 'control' : true }">
                            <input v-model="user.address" type="text" v-validate="'required'" name="address" :class="{ 'input' : true, 'is_danger' : errors.has('address')}" id="address" class="form-control input-sm" placeholder="Address">
                            <span v-show="errors.has('address')" class="help is-danger">{{ errors.first('address') }}</span>
                        </div>
                    </div>
                </div>

                <div class="row">
                    <div class="col-xs-6 col-sm-6 col-md-6">
                        <div class="form-group" :class="{ 'control' : true }">
                            <input v-model="user.postal_code" type="text"  v-validate="'required|alpha_num'" name="postal_code" :class="{ 'input' : true, 'is_danger' : errors.has('postal_code')}" id="postal_code" class="form-control input-sm" placeholder="Postal Code">
                            <span v-show="errors.has('postal_code')" class="help is-danger">{{ errors.first('postal_code') }}</span>
                        </div>
                    </div>
                    <div class="col-xs-6 col-sm-6 col-md-6">
                        <div class="form-group" :class="{ 'control' : true }">
                            <input v-model="user.phone_number" type="text" v-validate="'required'" name="phone_number" :class="{ 'input' : true, 'is_danger' : errors.has('phone_number')}" id="phone_number" class="form-control input-sm" placeholder="Phone Number">
                            <span v-show="errors.has('phone_number')" class="help is-danger">{{ errors.first('phone_number') }}</span>
                        </div>
                    </div>
                </div>
                

                <input type="button" @click='submitForm' value="Register" class="btn btn-info btn-block">

            </form>
        </div>
    </div>
    </transition>
</template>

<script>
    export default {
        data : function(){
            return {
                user : {
                    id : null,
                    firstname : null,
                    lastname: null,
                    username: null,
                    email : null,
                    password : null,
                    address : null,
                    postal_code : null,
                    phone_number : null
                },
                msg : null,
                alertClass : 'alert-danger'
            };
        },
        created(){
            let _self = this;

            if(this.$parent.userId==null){
                return;
            }
            this.user.id = this.$parent.userId;
            Vue.axios.get('/api/user/' + this.user.id, {} , this.$parent.tokenHeader ).then((response) => {
                let uData = response.data.data;

                _self.user = {
                    id : uData.id,
                    firstname : uData.firstname,
                    lastname:  uData.lastname,
                    username:  uData.username,
                    email :  uData.email,
                    password : null,
                    address :  uData.profile.address,
                    postal_code :  uData.profile.postal_code,
                    phone_number :  uData.profile.phone_number
                }

            });
            this.$parent.userId = null;
        },
        methods : {
            submitForm(){
                if(this.user.id!=null && this.user.id > 0){
                    this.updateUser();
                }else{
                    this.register();
                }
            },
            validateInput(callback){
                let _self = this;
                _self.$validator.validateAll().then((result) => {
                    if(!result){
                        alert('Please complete form.');
                        return;
                    }

                    callback(result);
                });
            },
            register(){
                let _self = this;
                _self.validateInput(function(){
                    Vue.axios.post('/api/user/add',  _self.user, _self.$parent.tokenHeader ).then((response) => {
                        _self.msg = 'Successfully Added';
                        _self.alertClass  =  'alert-success';
                        if(response.data.data.id)
                            _self.user.id = response.data.data.id
                    }).catch(error => {
                        _self.alertClass  =  'alert-danger';
                        if(error.response){
                            _self.msg = error.response.data.msg;
                        }else{
                            _self.msg = 'Error, please contact admin.';
                            console.log(error);
                        }

                    });
                })
            },
            updateUser(){
                let _self = this;
                _self.validateInput(function(){
                    Vue.axios.put('/api/user/' + _self.user.id + '/update',  _self.user, _self.$parent.tokenHeader ).then((response) => {
                        _self.msg = 'Successfully updated';
                        _self.alertClass  =  'alert-success';
                    }).catch(error => {
                        _self.alertClass  =  'alert-danger';
                        _self.msg = 'Updating error, Please contact admin';
                    });
                });
            },
            backToList(){
                this.$parent.showForm = false;
                this.$parent.showList = true;
            }
        }
    }
</script>