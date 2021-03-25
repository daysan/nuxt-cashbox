<template>
<div class="container">
      <form>
        <div class="mb-3">
            <label class="form-label">Сервер</label>
            <input type="text" class="form-control" v-model="host">
        </div>
        <div class="mb-3">
            <label class="form-label">Оператор (Кассир)</label>
            <input type="text" class="form-control" v-model="operator">
        </div>
        <div class="mb-3">
            <label class="form-label">Налоговая система</label>
            <select class="form-select form-control" v-model="taxationType">
                <option value="osn">Общая</option>
                <option value="usnIncome">Упрощенная (Доход)</option>
                <option value="usnIncomeOutcome">Упрощенная (Доход минус Расход)</option>
                <option value="envd">ЕНВД</option>
                <option value="usnIncomeOutcome">Единый сельскохозяйственный налог</option>
                <option value="patent">Патентная</option>
            </select>
        </div>
          <div class="mb-3">
            <label class="form-label">Налоговая ставка</label>
            <select class="form-select form-control" v-model="taxType">
                <option value="none">Налогом не облагается</option>
                <option value="vat0">НДС 0%</option>
                <option value="vat10">НДС 10%</option>
                <option value="vat18">НДС 18%</option>
                <option value="vat110">НДС 10/110</option>
                <option value="vat118">НДС НДС 18/118</option>
                <option value="vat20">НДС 20%</option>
                <option value="vat120">НДС 20/120</option>
            </select>
        </div>
        <div class="alert alert-success" role="alert" v-if="saved">
            Настройки сохранены
        </div>
        <button type="submit" class="btn btn-primary" v-on:click.prevent="saveSettings()">Сохранить</button>
    </form>
  </div>
</template>

<script>
export default {
    data() {
        return {
            host: (localStorage.getItem('atolHostSetting')) ? localStorage.getItem('atolHostSetting') : 'http://localhost:16732',
            taxationType: (localStorage.getItem('taxationTypeSetting')) ? localStorage.getItem('taxationTypeSetting') : 'osn',
            taxType: (localStorage.getItem('taxTypeSetting')) ? localStorage.getItem('taxTypeSetting') :'none',
            operator: (localStorage.getItem('operatorSetting')) ? localStorage.getItem('operatorSetting') : 'АДМИНИСТРАТОР',
            saved: false
        }
    },
    methods: {
        saveSettings: function() {
            let TH = this
            if(!this.host) {
                alert('Заполните поле Серевер')
                return false
            } else if(!this.operator) {
                alert('Заполните поле Оператор (Кассир)')
                return false
            }
            localStorage.setItem('atolHostSetting', this.host)
            localStorage.setItem('taxationTypeSetting', this.taxationType)
            localStorage.setItem('taxTypeSetting', this.taxType)
            localStorage.setItem('operatorSetting', this.operator)
            localStorage.setItem('activeSettings', true)
            this.saved = true
            setTimeout(function() {
                TH.saved = false
            }, 2000)
        }
    }
}
</script>

<style>

</style>
