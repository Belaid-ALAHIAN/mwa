<template>
  <div class="progressData">
    <chart class :options="bar" ref="chart" autoresize/>
  </div>
</template>
<style>
.progressData .echarts {
  width: 100% !important;
}
</style>

<script>
import gql from "graphql-tag";
import ECharts from "vue-echarts";
import "echarts/lib/chart/bar";
import moment from "moment";

export default {
  components: {
    chart: ECharts
  },
  data() {
    return {
      bar: {
        xAxis: {
          type: "category",
          data: []
        },
        yAxis: {
          type: "value",
          scale: true
        },
        tooltip: {},
        legend: {
          show: true,
          data: ["Votes"]
        },
        series: [
          {
            data: [],
            type: "bar"
          }
        ]
      }
    };
  },
  apollo: {
    beatsData: {
      query: gql`
        query beatsData($category: ID, $candidate: ID) {
          beatsData(category: $category, candidate: $candidate) {
            data {
              timestamp
              sum
            }
          }
        }
      `,
      variables() {
        return {
          category: this.$store.state.category,
          candidate: this.$store.state.candidate
        };
      },
      manual: true,
      result({ data, loading }) {
        if (!loading) {
          this.bar.xAxis.data = [];
          this.bar.series[0].data = [];
          data.beatsData.data.forEach(datum => {
            this.bar.xAxis.data.push(
              moment(datum.timestamp * 1000).format("HH:mm:ss")
            );
            this.bar.series[0].data.push(datum.sum);
          });
        }
      }
    },
    $subscribe: {
      // When a tag is added
      tags: {
        query: gql`
          subscription beatsData($category: ID, $candidate: ID) {
            beatsData(category: $category, candidate: $candidate) {
              timestamp
              sum
            }
          }
        `,
        // Reactive variables
        variables() {
          return {
            category: this.$store.state.category,
            candidate: this.$store.state.candidate
          };
        },
        // Result hook
        result(message) {
          // Let's update the local data
          this.bar.xAxis.data.shift();
          this.bar.series[0].data.shift();
          this.bar.xAxis.data.push(
            moment(message.data.beatsData.timestamp * 1000).format("HH:mm:ss")
          );
          this.bar.series[0].data.push(message.data.beatsData.sum);
          console.log(message.data.beatsData);
          // this.$refs.chart.refresh()
        }
      }
    }
  }
};
</script>