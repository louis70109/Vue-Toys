<script>
import SideBar from "../../components/sidebar.vue";
export default {
  name: "sign",
  components: {
    SideBar
  },
  data() {
    return {
      form: {
        user: {
          account: "",
          password: "",
          email: "",
          name: ""
        }
      },
      rules: {
        name: [
          { required: true, message: "請輸入姓名", trigger: "blur" },
          { min: 3, message: "輸入長度需大於 3", trigger: "blur" }
        ],
        account: [
          { required: true, message: "請輸入帳號", trigger: "blur" },
          { min: 5, message: "長度需大於 5", trigger: "blur" }
        ],
        email: [
          { required: true, message: "請輸入信箱", trigger: "blur" },
          {
            type: "email",
            message: "請輸入正確的信箱",
            trigger: ["blur", "change"]
          }
        ],
        password: [
          { required: true, message: "請輸入密碼", trigger: "blur" },
          { min: 6, message: "長度需大於 6", trigger: "blur" }
        ]
      },
      btnStatus: false,
      backend_ip: "https://iotser.iots.tw"
    };
  },
  methods: {
    submitForm(user) {
      this.$refs[user].validate(valid => {
        if (valid) {
          this.btnStatus = true;
          this.axios
            .post(`${this.backend_ip}/v1/users`, {
              account: this.form.user.account,
              password: this.$md5(this.form.user.password),
              email: this.form.user.email,
              name: this.form.user.name
            })
            .then(res => {
              if (res.data.status === true) {
                this.btnStatus = false;
                this.$localStorage.set("user_id", res.data.users.id);
                this.$localStorage.set("user_token", res.data.users.token);
                this.$router.push("/");
                this.$notify({
                  title: "註冊成功",
                  type: "success"
                });
              } else {
                alert("註冊失敗，請檢查欄位內容");
                this.btnStatus = false;
              }
            });
        } else {
          return false;
        }
      });
    }
  }
};
</script>

<template src="./template.html"></template>
<style lang="scss" src="./style.scss" scoped></style>
