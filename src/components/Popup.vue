<template>
    <div class="popup-wrapper">
        <div class="popup">
            <h2 class="popup__title">Налоговый вычет</h2>
            <p class="popup__p">Используйте налоговый вычет чтобы погасить 
                ипотеку досрочно. Размер налогового вычета
                составляет не более 13% от своего 
                официального годового дохода.</p>  
            <form @submit.prevent class="form">
                <div class="form__item">
                    <span class="form__text-label">
                        Ваша зарплата в месяц
                    </span>
                    <SalaryInput :class="{'invalid-input': isValide === false}"
                     v-model.number="salary" 
                     v-bind="salaryMaskConfig" 
                     type="text" placeholder="Введите данные" />
                    <p v-show="isValide === false" class="form__validation-text">Поле обязательно для заполнения</p>
                </div>
                <button @click="calculate" class="form__calculate-btn">Рассчитать</button>

                <div v-if="showCheckboxes" class="form__item form__checkboxes">
                    <span class="form__text-label">
                        Итого можете внести в качестве досрочных:
                    </span>

                    <label v-for="(row, index) in calculatedRows()" :key="index" class="form__checkbox-label">
                        <input class="form__checkbox" type="checkbox">
                        <span class="checkmark"></span>
                        <p class="form__checkbox-text"><span>{{ row }} рублей</span>{{ yearText(index + 1) }}</p>
                    </label>
                    
                </div>

                <div class="form__controls">
                    <span>Что уменьшаем?</span>
                    <div class="form__controls-buttons">
                        <button @click="modePayment" :class="{'mode-btn_active': mode === 'payment'}" class="mode-btn">Платеж</button>
                        <button @click="modeTime" :class="{'mode-btn_active': mode === 'time'}" class="mode-btn">Срок</button>
                    </div>

                </div>

                <button
                @click="submit"
                 :disabled="isValide !== true" class="form__submit">Добавить</button>
            </form>

            <button
            @click="close"
            title="Закрыть окно" class="popup__close">
                <img :src="require('../assets/close-modal.svg')" alt="">
            </button>
        </div>   
    </div>
</template>

<script>
import { ref } from 'vue';
import { Money3Component as SalaryInput } from 'v-money3';

export default {
    components: {
        SalaryInput,
    },
    setup(_, { emit }) {

        const salary = ref(null);
        const showCheckboxes = ref(false);
        const isValide = ref(null);

        const mode = ref('payment');

        function modePayment() {
            mode.value = 'payment';
        }

        function modeTime() {
            mode.value = 'time';
        }

        const salaryMaskConfig = {
            suffix: ' ₽',
            thousands: ' ',
            precision: 0,
            allowBlank: true
        }


        const amount = ref(260000);
        const payment = ref(0);

        function calculate() {



            if (salary.value > 0) {
                showCheckboxes.value = true;
                isValide.value = true;
                payment.value = (salary.value * 12) * 0.13;
            } else {
                isValide.value = false;
            }
            


        }

        function submit() {
            isValide.value = null;
            showCheckboxes.value = false;
            mode.value = 'payment';
            salary.value = null;
            close();
        }

        function close() {
            emit('close');
        }




        const calculatedRows = () => {


            let am = amount.value;

            let pay = Math.floor(payment.value); 
            let arr = [];
            while (am > 0) {
                if (am > pay) {
                    arr.push(pay)
                    am -= pay;
                } else {
                    arr.push(am);
                    am = 0;
                }
            }
            return arr;
        }; 

        function yearText(year) {
            if (year === 2) {
                return ' во 2-ой год'
            }
            let lastNumber = year.toString().split('').pop();
            if (lastNumber === '2' || lastNumber === '6' || lastNumber === '8' || lastNumber === '7') {
                return ` в ${year}-ой год`;
            } else if (lastNumber === '1' || lastNumber === '4' || lastNumber === '5' || lastNumber === '9' || lastNumber === '0') {
                return ` в ${year}-ый год`;
            } else if (lastNumber === '3') {
                return ` в ${year}-ий год`;
            }


        }


        return {
            close,
            salary,
            salaryMaskConfig,
            showCheckboxes,
            calculate,
            isValide,
            calculatedRows,
            modePayment,
            modeTime,
            mode,
            yearText,
            submit
        }
    }
}
</script>


<style lang="scss" scoped>

    .popup {
        top: 80px;
        background-color: #fff;
        border-radius: 30px;
        padding: 32px;
        position: relative;
        font-size: 0.875rem;
        align-self: start;
        display: flex;
        flex-direction: column;

        &-wrapper {
            padding: 0 30px;
            left: 0;
            right: 0;
            top: 0;
            bottom: 0;
            overflow-y: scroll;
            min-height: 100vh;
            display: flex;
            margin-bottom: 50px;
            justify-content: center;
            position: fixed;
            background-color: #B3B3B3;
        }
        &__title {
            margin-bottom: 16px;
            font-size: 1.75rem;
        }

        &__p {
            margin-bottom: 24px;
            color: #808080;
            max-width: 480px;
        }


        &__close {
            position: absolute;
            right: 27px;
            top: 27px;
            background: none;
            border: none;
        }

    }

    .form {
        display: flex;
        flex-direction: column;
        flex-grow: 1;

        &__text-label {
            font-weight: 500;
        }
        &__checkbox-text {
            color: #808080;
            span {
                color: #000000;
            }
        }
        &__validation-text {
            color: #EA0029;
            font-size: 0.75rem;
        }
        &__item {
            display: flex;
            flex-direction: column;
            gap: 8px;
            input {
                padding: 8px 10px;
                border: 1px solid #DFE3E6;
                border-radius: 3px;
                &:active {
                    border: 1px solid #EA0029;
                }

            }
        }

        &__checkbox-label {
            position: relative;
            padding-bottom: 16px;
            padding-left: 32px;
            user-select: none;
            border-bottom: 1px solid #DFE3E6;
            cursor: pointer;
            &:hover {
                .checkmark {
                    border: 1px solid #000000;
                }
            }
        }


        &__checkbox {
            position: absolute;
            opacity: 0;
            height: 0;
            width: 0;
            &:checked ~ .checkmark {
                background-color: #F64E4A;
                border: 1px solid transparent;
                background-image: url('../assets/checkbox-icon.svg');
            }
        }
        &__checkboxes {
            margin-bottom: 20px;
            display: flex;
            flex-direction: column;
            gap: 16px;
        }



        &__calculate-btn {
            margin-top: 8px;
            font-weight: 500;
            align-self: flex-start;
            background: none;
            border: none;
            color: #EA0029;
            margin-bottom: 24px;
        }

        &__controls {
            display: flex;
            flex-wrap: wrap;
            gap: 16px;
            align-items: center;
            margin-bottom: 40px;
            span {
                margin-right: 16px;
                font-weight: 500;
            }
            &-buttons {
                display: flex;
                gap: 16px;
            }
        }


        &__submit {
            border-radius: 6px;
            padding: 16px;
            color: #fff;
            margin-top: auto;
            box-shadow: 0px 0px 24px rgba(234, 0, 41, 0.33);
            border: none;
            background-color: #EA0029;
            background: linear-gradient(255.35deg, #DC3131 0.83%, rgba(255, 79, 79, 0) 108.93%), #FF5E56;
            &:hover {
                background: #EA0029;
            }
            &:disabled {
                background: #BEC5CC;
                box-shadow: none;
                cursor: not-allowed;
            }
        }

    }


.checkmark {
    position: absolute;
    left: 0;
    top: -2px;
    width: 20px;
    height: 20px;
    border-radius: 6px;
    margin-right: 10px;
    background-repeat: no-repeat;
    border: 1px solid #DFE3E6;
    background-position: center center;
    background-size: 66% 66%;
}


.mode-btn {
    padding: 6px 12px;
    border-radius: 50px;
    border: none;
    background: #EEF0F2;
    color: #000000;
    &:hover {
        background-color: #EA0029;
        color: #fff;
    }
    &_active {
        background-color: #F64E4A;
        color: #fff;
        background: linear-gradient(255.35deg, #DC3131 0.83%, rgba(255, 79, 79, 0) 108.93%), #FF5E56;
    }
}

.form__item .invalid-input {
    border: 1px solid #EA0029;
}

@media (max-width: 576px) {
    .popup {
        height: auto;
        min-height: 100vh;
        width: 100%;
        top: 0;
        border-radius: 0;
        &-wrapper {
            padding: 0;
            position: absolute;
        }
    }
}
    
</style>