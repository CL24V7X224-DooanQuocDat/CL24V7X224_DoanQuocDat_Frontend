<template>
  <Form @submit="submitContact" :validation-schema="contactFormSchema">
    <div class="form-group">
      <label for="name">Tên</label>
      <Field
        name="name"
        type="text"
        class="form-control"
        v-model="contactLocal.name"
      />
      <ErrorMessage name="name" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="email">E-mail</label>
      <Field
        name="email"
        type="email"
        class="form-control"
        v-model="contactLocal.email"
      />
      <ErrorMessage name="email" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="address">Địa chỉ</label>
      <Field
        name="address"
        type="text"
        class="form-control"
        v-model="contactLocal.address"
      />
      <ErrorMessage name="address" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="phone">Điện thoại</label>
      <Field
        name="phone"
        type="tel"
        class="form-control"
        v-model="contactLocal.phone"
      />
      <ErrorMessage name="phone" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="company">Công ty</label>
      <Field
        name="company"
        type="text"
        class="form-control"
        v-model="contactLocal.company"
      />
      <ErrorMessage name="company" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="notes">Ghi chú</label>
      <Field
        name="notes"
        as="textarea"
        class="form-control"
        v-model="contactLocal.notes"
      />
      <ErrorMessage name="notes" class="error-feedback" />
    </div>

    <div class="form-group form-check">
      <input
        name="favorite"
        type="checkbox"
        class="form-check-input"
        v-model="contactLocal.favorite"
      />
      <label for="favorite" class="form-check-label">
        <strong>Liên hệ yêu thích</strong>
      </label>
    </div>

    <div class="form-group form-check">
      <input
        name="colleague"
        type="checkbox"
        class="form-check-input"
        v-model="contactLocal.colleague"
      />
      <label for="colleague" class="form-check-label">
        <strong>Đồng nghiệp</strong>
      </label>
    </div>

    <div class="form-group">
      <button class="btn btn-primary">
        <i class="fas fa-save"></i> Lưu
      </button>
      <button
        v-if="contactLocal._id"
        type="button"
        class="ml-2 btn btn-danger"
        @click="deleteContact"
      >
        <i class="fas fa-trash"></i> Xóa
      </button>
      <button type="button" class="ml-2 btn btn-danger" @click="Cancel">
        <i class="fas fa-times"></i> Thoát
      </button>
    </div>
  </Form>
</template>

<script>
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";

export default {
  components: {
    Form,
    Field,
    ErrorMessage,
  },
  emits: ["submit:contact", "delete:contact"],
  props: {
    contact: { type: Object, required: true },
  },
  data() {
    const contactFormSchema = yup.object().shape({
      name: yup
        .string()
        .required("Tên phải có giá trị.")
        .min(2, "Tên phải ít nhất 2 ký tự.")
        .max(50, "Tên có nhiều nhất 50 ký tự."),
      email: yup
        .string()
        .email("E-mail không đúng.")
        .max(50, "E-mail tối đa 50 ký tự."),
      address: yup.string().max(100, "Địa chỉ tối đa 100 ký tự."),
      phone: yup
        .string()
        .matches(
          /((09|03|07|08|05)+([0-9]{8})\b)/g,
          "Số điện thoại không hợp lệ."
        ),
      // Thêm validation cho các trường mới (đặt là optional)
      company: yup.string().max(100, "Công ty tối đa 100 ký tự."),
      notes: yup.string().max(500, "Ghi chú tối đa 500 ký tự."),
      colleague: yup.boolean(),
    });
    return {
      // SỬA LỖI QUAN TRỌNG:
      // Phải tạo bản sao của prop 'contact' để tránh sửa trực tiếp prop.
      // Sử dụng toán tử spread '...' để tạo một object mới.
      contactLocal: { ...this.contact },
      contactFormSchema,
    };
  },
  methods: {
    submitContact() {
      // Gửi 'contactLocal' (bản sao đã được chỉnh sửa) lên component cha
      this.$emit("submit:contact", this.contactLocal);
    },
    deleteContact() {
      this.$emit("delete:contact", this.contactLocal.id);
    },
    Cancel() {
      const reply = window.confirm(
        "You have unsaved changes! Do you want to leave?"
      );
      if (!reply) {
        // stay on the page if
        // user clicks 'Cancel'
        return false;
      } else {
        this.$router.push({ name: "contactbook" });
      }
    },
  },
};
</script>

<style scoped>
@import "@/assets/form.css";
</style>