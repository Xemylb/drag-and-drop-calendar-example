<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <table v-if="rooms.length > 0">
      <thead>
        <tr>
          <th>Rooms name</th>
          <th v-for="({ date }, index) in rooms[0].dates" :key="index">{{ date }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="{id, name, dates} in rooms" :key="id">
          <th>{{name}}</th>
          <th v-for="({ date, events }, index) in dates" :key="index">
            <draggable v-model="dates[index].events" draggable=".event" group='events'>
              <div :style="{width:`${element.duration * tdWidth}px` }" v-for="element in events" :key="element.name" @click='eventClick()' class="event">
                {{element.name}}
              </div>
            </draggable>
          </th>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
    import draggable from 'vuedraggable'
export default {
  name: "HelloWorld",
    components: {
        draggable,
    },
  props: {
    msg: String
  },
  data: () => ({
      tdWidth: 140,
      tdHeight: 50,
    prevDate: "",
    nextDate: "",
    rooms: [{
      id: 1,
      name: 'Room1',
      dates: [],
    },
    {
      id: 2,
      name: 'Room2',
      dates: [],
    }],
      eventsList:[
          {
              date: '21/10/2019',
              duration: '2',
              name: 'Event',
              roomId: 1,
          },
          {
              date: '24/10/2019',
              duration: '4',
              roomId: 2,
              name: 'Event2'
          },
          {
              date: '22/10/2019',
              duration: '3',
              roomId: 2,
              name: 'Event2'
          }
      ]
  }),
  async mounted() {
    await this.getRangeDated();
    await this.setEventsToDates();
  },
  methods: {
    getRangeDated() {
      this.prevDate = this.$moment().subtract(15, "days");
      this.nextDate = this.$moment().add(15, "days");
      const momentStart = this.$moment(this.prevDate._d).format("MM-DD-YYYY");
      const momentEnd = this.$moment(this.nextDate._d).format("MM-DD-YYYY");

      

      //Logic for getting rest of the dates between two dates("FromDate" to "EndDate")
      this.rooms.map(room => {
//creating JS date objects
      let start = new Date(momentStart);
      let end = new Date(momentEnd);
      while (start < end) {
        const dateObj = {
          date: this.$moment(start).format("DD/MM/YYYY"),
          events: []
        }
        room.dates.push(dateObj);
        let newDate = start.setDate(start.getDate() + 1);
        start = new Date(newDate);
      }
      })
      
    },
    eventClick(){
      alert('click')
    },
    setEventsToDates(){
      this.eventsList.map((event)=>{
          this.rooms.map(({id, dates}) => {
            dates.map((item, index) => {
              if(event.date === item.date && event.roomId === id){
                dates[index].events.push(event)
              }
            })
          })
      })
    },
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
*,
:after,
:before {
  box-sizing: border-box;
}
.hello {
  max-width: 100%;
  overflow: auto;
}
table {
  border-collapse: collapse;
  th {
    position: relative;
    border: 1px solid;
    min-width: 140px;
    max-width: 140px;
    max-height: 50px;
    min-height: 50px;
    &>div{
    padding: 10px;
    }
  }
}
.event{
  position: absolute;
  left: 0;
  top:0;
  height: 100%;
  background-color: aqua;
  max-height: 50px;
  z-index: 2;
}
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
