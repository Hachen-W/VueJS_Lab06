<template>
  <div class="registration-container">
    <h2>Регистрация</h2>
    
    <form @submit.prevent="onSubmit">
      <div class="form-group">
        <label for="email">Электронная почта (Email)</label>
        <input
          id="email"
          v-model="emailValue"
          type="text"
          :class="emailClass"
          placeholder="example@domain.com"
        />
        <span v-if="emailValue && emailError" class="error-text">{{ emailError }}</span>
      </div>

      <div class="password-section">
        <div class="form-group password-input-container">
          <label for="password">Пароль (Password)</label>
          <input
            id="password"
            v-model="passwordValue"
            type="password"
            :class="passwordClass"
            placeholder="Введите надежный пароль"
          />
        </div>

        <div class="criteria-box">
          <h4>Критерии пароля:</h4>
          <ul>
            <li :class="passwordCriteria.length ? 'valid' : 'invalid'">
              ➔ Длина не менее 8
            </li>
            <li :class="passwordCriteria.number ? 'valid' : 'invalid'">
              ➔ Цифры
            </li>
            <li :class="passwordCriteria.lowercase ? 'valid' : 'invalid'">
              ➔ Буквы нижнего регистра
            </li>
            <li :class="passwordCriteria.uppercase ? 'valid' : 'invalid'">
              ➔ Буквы верхнего регистра
            </li>
            <li :class="passwordCriteria.special ? 'valid' : 'invalid'">
              ➔ Спецсимволы
            </li>
          </ul>
        </div>
      </div>

      <div class="form-group checkbox-group">
        <label class="checkbox-label">
          <input type="checkbox" v-model="agreementValue" />
          <span>I agree with license agreement.</span>
        </label>
      </div>

      <button type="submit" :disabled="isButtonDisabled" class="submit-btn">
        Зарегистрироваться
      </button>
    </form>
  </div>
</template>

<script setup>
import { computed } from 'vue';
import { useForm, useField } from 'vee-validate';

const { handleSubmit } = useForm();

const validateEmail = (value) => {
  if (!value) return 'Поле обязательно для заполнения';
  // Регулярное выражение для проверки корректности email
  const regex = /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i;
  return regex.test(value) ? true : 'Некорректный формат email адреса';
};

const validatePassword = (value) => {
  if (!value) return 'Пароль обязателен';
  
  const hasLength = value.length >= 8;
  const hasNumber = /\d/.test(value);
  const hasLowercase = /[a-z]/.test(value);
  const hasUppercase = /[A-Z]/.test(value);
  const hasSpecial = /[!@#$%^&*(),.?":{}|<>_+\-=\[\]\\\/~`]/.test(value);

  if (hasLength && hasNumber && hasLowercase && hasUppercase && hasSpecial) {
    return true;
  }
  return 'Пароль слишком слабый';
};

const { value: emailValue, errorMessage: emailError } = useField('email', validateEmail);
const { value: passwordValue, errorMessage: passwordError } = useField('password', validatePassword);
const { value: agreementValue } = useField('agreement');

const passwordCriteria = computed(() => {
  const currentPassword = passwordValue.value || '';
  return {
    length: currentPassword.length >= 8,
    number: /\d/.test(currentPassword),
    lowercase: /[a-z]/.test(currentPassword),
    uppercase: /[A-Z]/.test(currentPassword),
    special: /[!@#$%^&*(),.?":{}|<>_+\-=\[\]\\\/~`]/.test(currentPassword)
  };
});

const emailClass = computed(() => {
  if (!emailValue.value) return ''; // Если поле пустое - никак не подсвечивается
  return emailError.value ? 'border-invalid' : 'border-valid';
});

const passwordClass = computed(() => {
  if (!passwordValue.value) return ''; // Если поле пустое - никак не подсвечивается
  return passwordError.value ? 'border-invalid' : 'border-valid';
});

const isButtonDisabled = computed(() => {
  const isEmailValid = emailValue.value && !emailError.value;
  const isPasswordValid = passwordValue.value && !passwordError.value;
  const isAgreementChecked = !!agreementValue.value;

  return !(isEmailValid && isPasswordValid && isAgreementChecked);
});

const onSubmit = handleSubmit((values) => {
  alert('Форма успешно валидирована и отправлена!');
  console.log('Данные формы:', values);
});
</script>

<style>
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  background-color: #f4f6f9;
  margin: 0;
  padding: 50px 15px;
  display: flex;
  justify-content: center;
}

.registration-container {
  background: #ffffff;
  max-width: 700px;
  width: 100%;
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

h2 {
  margin-top: 0;
  color: #212529;
  border-bottom: 2px solid #dee2e6;
  padding-bottom: 10px;
  margin-bottom: 25px;
}

.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

.form-group label {
  font-weight: 600;
  margin-bottom: 8px;
  color: #495057;
  font-size: 14px;
}

.form-group input[type="text"],
.form-group input[type="password"] {
  padding: 10px 14px;
  font-size: 15px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  outline: none;
  transition: border-color 0.2s ease;
}

.password-section {
  display: flex;
  gap: 25px;
  align-items: flex-start;
  margin-bottom: 20px;
}

.password-input-container {
  flex: 1;
  margin-bottom: 0;
}

.criteria-box {
  background-color: #f8f9fa;
  border: 1px solid #e9ecef;
  border-radius: 6px;
  padding: 15px;
  min-width: 250px;
}

.criteria-box h4 {
  margin: 0 0 10px 0;
  font-size: 14px;
  color: #495057;
}

.criteria-box ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.criteria-box li {
  font-size: 13px;
  margin-bottom: 6px;
  font-weight: 500;
  transition: color 0.2s ease;
}

.border-valid {
  border: 2px solid #28a745 !important;
}

.border-invalid {
  border: 2px solid #dc3545 !important;
}

.valid {
  color: #28a745;
}

.invalid {
  color: #dc3545;
}

.error-text {
  color: #dc3545;
  font-size: 12px;
  margin-top: 5px;
}

.checkbox-group {
  margin-bottom: 25px;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  font-weight: normal !important;
  font-size: 14px;
}

.checkbox-label input {
  width: 16px;
  height: 16px;
  cursor: pointer;
}

.submit-btn {
  width: 100%;
  padding: 12px;
  font-size: 16px;
  font-weight: 600;
  color: white;
  background-color: #007bff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.submit-btn:hover:not(:disabled) {
  background-color: #0056b3;
}

.submit-btn:disabled {
  background-color: #6c757d;
  opacity: 0.65;
  cursor: not-allowed;
}
</style>
