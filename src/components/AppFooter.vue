<template>
  <footer class="fixed-bottom text-center">
    <hr/>
    <p>Copyright &copy; {{ currentYear }} </p>
  </footer>
</template>

<script>
export default {
  data() {
    return {
      currentYear: new Date().getFullYear(),
    };
  }
};
</script>
