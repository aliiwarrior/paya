<template>
  <div>
    <div class="headline-container">
      <div class="headline">فاکتور ها</div>
      <nuxt-link
        :to="`/admin/orders/${$route.params.customerId}/`"
        class="btn btn-sm btn-back"
        >بازگشت</nuxt-link
      >
    </div>

    <div class="head">
      <nuxt-link to="/admin/orders" class="head">لیست مشتریان</nuxt-link>
      <span> </span>
      <i class="fa fa-angle-left" aria-hidden="true"></i>
      <span> </span>
      <span v-for="u in users" :key="u.id">
        <nuxt-link
          :to="`/admin/orders/${$route.params.customerId}/`"
          class="head"
          v-if="u.id == $route.params.customerId"
          >سفارشات({{ u.first_name }})</nuxt-link
        >
      </span>
      <span> </span>
      <i class="fa fa-angle-left" aria-hidden="true"></i>
      <span> </span>
      <span>{{ order.title }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AdminPageConnectUserSeller',
  layout: 'admin',
  async asyncData({ $axios, params }) {
    const files = await $axios.$get(
      `/managers/api/orders/file/detail/${params.orderId}/`
    )
    const order = await $axios.$get(
      `/managers/api/rows/detail/${params.orderId}/`
    )
    const users = await $axios.$get(
      `managers/api/users/detail/${params.customerId}/`
    )
    return {
      files,
      order: order[0],
      users,
    }
  },
  methods: {
    async onSubmit() {
      const bodyFormData = new FormData()
      bodyFormData.append('file', this.$refs.fileInput.files[0])
      bodyFormData.append('order', +this.$route.params.orderId)

      try {
        const res = await this.$axios.post(
          `/managers/api/orders/file/create/`,
          bodyFormData,
          {
            headers: {
              'Content-Type': 'multipart/form-data',
            },
          }
        )
        console.log(res)
        this.$router.push(`/admin/orders/${this.$route.params.customerId}`)
      } catch (ex) {
        console.log(ex)
      }
    },

    async onDelete(id) {
      try {
        const res = await this.$axios.delete(
          `/managers/api/orders/file/delete/${id}/`
        )

        console.log(res)
        this.$router.push(`/admin/orders/${this.$route.params.customerId}/`)
      } catch (ex) {
        console.log(ex)
      }
    },
  },
}
</script>

<style scoped>
.message-box {
  min-height: 170px;
  resize: none;
}
</style>
