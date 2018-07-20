<template>
  <div class="animated fadeIn">
    <Row>
      <Col :sm="24" :md="24" :lg="12">
      <h3>newStart</h3>
      <p>底层框架</p>
      </Col>
    </Row>
    <Table :columns="columns1" :data="data1"></Table>

  </div>
</template>

<script>
export default {
  data() {
    return {
      columns1: [
        {
          title: "姓名",
          key: "name"
        },
        {
          title: "年龄",
          key: "age"
        },
        {
          title: "地址",
          key: "address"
        },
        {
          title: "操作",
          key: "action",
          width: 150,
          align: "center",
          render: (h, params) => {
            return h("div", [
              h(
                "Button",
                {
                  props: {
                    type: "primary",
                    size: "small"
                  },
                  style: {
                    marginRight: "5px"
                  },
                  on: {
                    click: () => {
                      this.show(params.index);
                    }
                  }
                },
                "查看"
              ),
              h(
                "Button",
                {
                  props: {
                    type: "error",
                    size: "small"
                  },
                  on: {
                    click: () => {
                      this.remove(params.index);
                    }
                  }
                },
                "删除"
              )
            ]);
          }
        }
      ],
      data1: [
        {
          name: "王小明",
          age: "11",
          address: "广州是天河区"
        },
        {
          name: "李小红",
          age: "12",
          address: "广州市黄埔区"
        },
        {
          name: "baiyang",
          age: "26",
          address: "万达广场"
        },
        {
          name: "123",
          age: "321",
          address: "123345"
        }
      ]
    };
  },
  methods: {
    show(index) {
      this.$Modal.info({
        title: "用户信息",
        content: `姓名:${this.data1[index].name}<br>年龄:${
          this.data1[index].age
        }<br>地址:${this.data1[index].address}`
      });
    },
    remove(index){
      this.data1.splice
    }
  }
};
</script>
