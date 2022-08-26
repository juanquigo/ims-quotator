<template>
  <div id="quotator">
  </div>
  <div class="button-container">
    <button @click="sendQuotation">
      Enviar
    </button>
  </div>
</template>

<script setup>
import { onMounted, onBeforeUnmount } from 'vue'

onMounted(() => {
  let scriptUrl
  if (!customElements.get('ims-quotation')) {
    scriptUrl = document.createElement('script')
    scriptUrl.setAttribute('src', 'https://assets-ims.staging.lahaus.com/ims-quotation.min.js')
    document.head.appendChild(scriptUrl)
  }

  const component = document.createElement('ims-quotation')
  component.setAttribute('id', 'wc-quotator')
  component.setAttribute('token', 'e71d122e33522728b3a8')
  component.setAttribute('locale', 'co') // También pude ser 'mx'
  component.setAttribute('sections', 'default_plan,quote_table')
  component.setAttribute('catalog-id', '01FDZFDYSJX25830S0DRT4SWH2')
  component.setAttribute('plan-name', 'Lotus')
  component.setAttribute('customer-first-name', 'Juanse')
  component.setAttribute('customer-last-name', 'Quintero')
  component.setAttribute('customer-email', 'juanquintero@lahaus.com')
  component.setAttribute('customer-celphone', '+573217739619')
  component.addEventListener('ims-quotation-ok', onQuotationOk)
  component.addEventListener('ims-quotation-error', onQuotationError)
  
  const parentElement = document.getElementById('quotator')
  parentElement.appendChild(component)
})

onBeforeUnmount(() => {
  const imsQuotation = document.getElementById('wc-quotator')
  imsQuotation.removeEventListener('ims-quotation-ok', onQuotationOk)
  imsQuotation.removeEventListener('ims-quotation-error', onQuotationError)
  imsQuotation.dispatchEvent(new CustomEvent('ims-destroy', { bubbles: true }))
})

function onQuotationOk({ detail }) {
  const [{ response }] = detail
  alert(JSON.stringify(response))
}

function onQuotationError ({ detail }) {
  const [{ error }] = detail
  alert(JSON.stringify(error))
}

function sendQuotation () {
  const sendEvent = new CustomEvent('ims-send', {
    bubbles: true,
    composed: true,
    detail: {
      quote_language: 'es-CO', // 'es-CO', 'es-MX', 'en-US'
      sendEmail: false
    }
  })

  const imsQuotation = document.getElementById('wc-quotator')
  imsQuotation.dispatchEvent(sendEvent)
}
</script>