<script lang="js">
  const API_USERS = `https://randomuser.me/api/?results=5`;
  const formatDate = (date) => {
    const intmd = date.split("T");
    const gmt = `${intmd[0].split("-").join("/")} ${intmd[1].split(".")[0]} GMT`;
    const newDate = new Date(gmt);
    return {
      date,
      gmt,
      newDate,
      day: newDate.getUTCDate(),
      month: newDate.toLocaleString('en-us', {
        month: "long"
      }),
      year: newDate.getFullYear(),
      text: `${1 + newDate.getUTCMonth()}/${newDate.getUTCDate()}/${newDate.getFullYear()}`
    }
  }
  export default {
    data() {
      return {
        items: [],
        error: false,
        currentHeight: null,
        isFetching: false,
      }
    },
    methods: {
      onScroll() {
        let self = this;
        const windowHeight =
          'innerHeight' in window ?
          window.innerHeight :
          document.documentElement.offsetHeight;
        const body = document.body;
        const html = document.documentElement;
        const docHeight = Math.max(
          body.scrollHeight,
          body.offsetHeight,
          html.clientHeight,
          html.scrollHeight,
          html.offsetHeight
        );
        const windowBottom = windowHeight + window.pageYOffset;
        if (Math.floor(windowBottom) >= docHeight && self.items.length < 20) {
          if (!self.isFetching) {
            self.isFetching = true;
            self.getData().then(() => {
              self.isFetching = false;
            })
          }
        }
      },
      getData() {
        let self = this;
        const request = new Request(API_USERS);
        return fetch(request).then(res => res.json()).then(json => {
          self.items = self.items.concat(json.results);
          for (let item of self.items) {
            item.displayName =
              `${item.name.title[0].toUpperCase()}${item.name.title.substring(1)}. ${item.name.first[0].toUpperCase()}${item.name.first.substring(1)} ${item.name.last[0].toUpperCase()}${item.name.last.substring(1)}`;
            item.joinDate = formatDate(item.registered.date);
          }
        }).catch(() => this.error = true)
      }
    },
    beforeMount() {
      window.addEventListener('scroll', this.onScroll);
    },
    beforeDestroy() {
      window.removeEventListener('scroll', this.onScroll);
    },
    mounted() {
      this.getData();
    }
  }
</script>

<style lang="css">
  
  ul {
    margin: 0;
    padding: 0;
  }
  
  ul>li {
    list-style: none;
    padding: 10px;
    height: 120px;
    margin: 15px;
    background: #ffffff;
    border-radius: 4px;
    box-shadow: -1px 13px 20px rgba(0, 0, 0, 0.075);
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }
  
  ul>li>.img {
    height: 100%;
    width: 100px;
    border-radius: 4px 0 0 4px;
  }
  
  ul>li>.rightContainer>div {
    margin: 10px;
    text-align: right;
  }
  
  ul>li .rightContainer>.name {
    font-size: 16px;
    font-weight: 500;
  }
  
  ul>li .rightContainer>.registeredDate {
    font-size: 12px;
  }
</style>

<template lang="pug">
ul
  li(v-for="item in items")
    img(:src="item.picture.large")
    .rightContainer
      .name {{ item.displayName }}
      .registeredDate Registered Date {{`${item.joinDate.day} ${item.joinDate.month}`}}
</template>