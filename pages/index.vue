<template>
  <div>
    <div v-html="renderPage()"></div>
  </div>
</template>

<script>
export default {
  mounted() {
    addEventListener('popstate', () => {
      this.rerender(location.pathname.substr(1))
      this.addListeners()
    })
  },
  data() {
    return {
      product: {
        name: 'Tractor',
        variants: [
          {
            sku: 't_porsche',
            color: 'red',
            name: 'Porsche-Diesel Master 419',
            image: 'image/tractor-red.jpg',
            thumb: 'image/tractor-red-thumb.jpg',
            price: '66,00 €',
          },
          {
            sku: 't_fendt',
            color: 'green',
            name: 'Fendt F20 Dieselroß',
            image: 'image/tractor-green.jpg',
            thumb: 'image/tractor-green-thumb.jpg',
            price: '54,00 €',
          },
          {
            sku: 't_eicher',
            color: 'blue',
            name: 'Eicher Diesel 215/16',
            image: 'image/tractor-blue.jpg',
            thumb: 'image/tractor-blue-thumb.jpg',
            price: '58,00 €',
          },
        ],
      },
      // $app: document.getElementById('app'),
    }
  },
  methods: {
    renderOption(variant, sku) {
      const active = sku === variant.sku ? 'active' : ''
      return `
    <a href="/${variant.sku}" class="${active}" type="button" data-sku="${variant.sku}">
      <img src="${variant.thumb}" alt="${variant.name}" />
    </a>
  `
    },
    renderPage(sku = 't_porsche') {
      const variant = this.product.variants.find((v) => sku === v.sku)
      if (!variant) {
        return '<pre>no product not found</pre>'
      }
      return `
    <h1 id="store">The Model Store</h1>
    <blue-basket id="basket"><!--#include virtual="/blue-basket" --></blue-basket>
    <div id="image"><div><img src="${variant.image}" alt="${
        variant.name
      }" /></div></div>
    <h2 id="name">${this.product.name} <small>${variant.name}</small></h2>
    <div id="options">${this.product.variants
      .map((v) => this.renderOption(v, sku))
      .join('')}</div>
    <blue-buy id="buy" sku="${
      variant.sku
    }"><!--#include virtual="/blue-buy?sku=${encodeURIComponent(
        variant.sku
      )}" --></blue-buy>
    <green-recos id="reco" sku="${
      variant.sku
    }"><!--#include virtual="/green-recos?sku=${encodeURIComponent(
        variant.sku
      )}" --></green-recos>
  `
    },
    handleClickOption(e) {
      e.preventDefault()
      const sku = e.currentTarget.getAttribute('data-sku')
      window.history.pushState(null, null, sku)
      this.rerender(sku)
    },
    addListeners() {
      const $btns = document
        .getElementById('app')
        .querySelectorAll('#options a')
      Array.prototype.forEach.call($btns, ($btn) =>
        $btn.addEventListener('click', this.handleClickOption)
      )
    },
    removeListeners() {
      const $btns = document
        .getElementById('app')
        .querySelectorAll('#options a')
      Array.prototype.forEach.call($btns, ($btn) =>
        $btn.removeEventListener('click', this.handleClickOption)
      )
    },
    rerender(sku) {
      this.removeListeners()
      document.getElementById('app').innerHTML = this.renderPage(sku)
      this.addListeners()
    },
  },
}
</script>

<style lang="scss" scoped></style>
