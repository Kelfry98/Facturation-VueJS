<template>
<div >
  <div class="titlee">
    <span class="container">
      <br>
      {{addTitle }}
      <br><br>
    </span>
    
  </div>
  <hr>
  <div class="margin">
    <Form class="container" ref="formValidate" :model="formValidate" :rules="ruleValidate" :label-width="80">
        <Row>
         <Col span="12">
          <div :style="{margin: '20px', padding: '2%'}">
        <FormItem  prop="name">
          <label for="">Nombre:</label>
            <Input v-model="formValidate.name"  clearable size="large"></Input>
        </FormItem>
        <FormItem  prop="lastName">
          <label for="">Apellidos:</label>
            <Input prefix="ios-checkmark" v-model="formValidate.lastName"  clearable size="large"></Input>
        </FormItem>
         <FormItem  prop="dni">
           <label for="">Cedula:</label>
            <Input type="text" v-model="formValidate.dni"  clearable size="large"></Input>
        </FormItem>
        <FormItem  prop="userName">
          <label for="">Nombre de Usuario:</label>
            <Input type="text" v-model="formValidate.userName" clearable size="large"></Input>
        </FormItem>
           <FormItem  prop="authorize">
           <label for="">Rol:</label><br>
            <Select v-model="formValidate.authorize" size="large" placeholder="Seleccionar" :style="{width:'50%'}">
                <Option value="employee">1</Option>
                <Option value="admin">2</Option>
            </Select>
        </FormItem>
         </div>
        </Col>
        <Col span="12" >
        <div :style="{margin: '20px', padding: '2%'}">
        <FormItem  prop="email">
          <label for="">E-mail:</label>
            <Input v-model="formValidate.email"  clearable size="large"></Input>
        </FormItem>
         <FormItem  prop="address">
           <label for="">Direccion:</label>
            <Input v-model="formValidate.address"  clearable size="large"></Input>
        </FormItem>
         <FormItem  prop="telephone">
           <label for="">Telefono:</label>
            <Input type="text" v-model="formValidate.telephone"  clearable size="large" ></Input>
        </FormItem>
        <FormItem  prop="password">
          <label for="">Contraseña:</label>
            <Input type="password" v-model="formValidate.password" clearable size="large">
            </Input>
        </FormItem>
        
        </div>
         </Col>
       
         </Row>
         <Row>
           <Col span="6" offset="9">
             <FormItem>
            <Button type="success" size="large" @click="handleSubmit('formValidate')">Enviar</Button>
            <Button  @click="handleReset('formValidate')"size="large" >Limpiar</Button>
        </FormItem>
           </Col>
           
         </Row>
    </Form>
    </div>
   </div>
</template>
<script>
export default {
  data() {
    return {
      formValidate: {
        id: 0,
        name: null,
        email: null,
        lastName: null,
        address: null,
        dni: null,
        telephone: null,
        userName: null,
        password: null,
        authorize: 1
      },
      ruleValidate: {
        name: [
          {
            required: true,
            message: "Campo vacio!",
            trigger: "blur"
          }
        ],
        lastName: [
          {
            required: true,
            message: "Campo vacio!",
            trigger: "blur"
          }
        ],
        address: [
          {
            required: true,
            message: "Campo vacio!",
            trigger: "blur"
          }
        ],
        dni: [
          {
            required: true,
            message: "Campo vacio!",
            trigger: "blur"
          }
        ],
        telephone: [
          {
            required: true,
            message: "Campo vacio!",
            trigger: "blur"
          }
        ],
        userName: [
          {
            required: true,
            message: "Campo vacio!"
          }
        ],
        password: [
          {
            required: true,
            message: "Campo vacio!",
            trigger: "blur"
          },
          {
            min: 8,
            message: "Su contraseña debe tener minimo 8 digitos",
            trigger: "blur"
          }
        ],
        email: [
          {
            required: true,
            message: "Campo vacio!",
            trigger: "blur"
          },
          {
            type: "email",
            message: "Formato del correo incorrecto",
            trigger: "blur"
          }
        ]
      }
    };
  },
  computed: {
    addTitle() {
      let iam = this;
      return iam.formValidate.id === 0
        ? "Agregar Empleado"
        : "Editar Empleado";
    }
  },
  created() {
    this.get(this.$route.params.id)
  },
  methods: {
    get(id){
      if (id == undefined) {
        return
      }
      let iam = this;
      iam.$store.state.services.UserService.get(id)
      .then(t =>{
        iam.formValidate.id = t.data.id
        iam.formValidate.authorize = t.data.authorize
        iam.formValidate.password = t.data.password
        iam.formValidate.userName = t.data.userName
        iam.formValidate.telephone = t.data.telephone
        iam.formValidate.dni = t.data.dni
        iam.formValidate.address = t.data.address
        iam.formValidate.email = t.data.email
        iam.formValidate.name = t.data.name
        iam.formValidate.lastName = t.data.lastName
      }).catch(t=>{
        iam.$Message.error("Error!");
      })
    },
    handleSubmit() {
      let iam = this;
      iam.$refs["formValidate"].validate(valid => {
        if (valid) {
          if (iam.formValidate.id >0) {
            iam.$store.state.services.UserService.updateT(iam.formValidate)
            .then(r => {
              iam.$Message.success("Empleado editado!");
              iam.$router.push("/employee/see");
            })
            .catch(r => {
              iam.$Message.error("Error al editar!");
            });
          } else {
            iam.$store.state.services.UserService.add(iam.formValidate)
            .then(r => {
              iam.$Message.success("Empleado agregado!");
              iam.$router.push("/employee/see");
            })
            .catch(r => {
              iam.$Message.error("Error al agregar!");
            });
          }
          
        } else {
          iam.$Message.error("Error!");
        }
      });
    },
    handleReset(formValidate) {
      let iam = this;
      iam.$refs[formValidate].resetFields();
    }
  }
};
</script>
<style>
label {
  font-family: "Verdana";
  font-size: 14px;
}
form {
  border-radius: 5px;
  background-color: #f8f8f9;
  padding: 20px;
  box-shadow: 0 1.5px 0px 0 rgba(0, 0, 0, 0.2), 0 0px 4px 0 rgba(0, 0, 0, 0.19);
}
label {
  font-weight: bolder;
}
.titlee {
  font-weight: bolder;
  font-size: 18px;
  text-align: center;
  background-color: #424242;
  color: #f8f8f9;
}
.margin {
  margin-top: 2%;
}
hr {
  color: #e8eaec;
}
</style>

