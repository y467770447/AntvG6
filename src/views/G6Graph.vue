<template>
<div>
  <div>G6-Graph</div>
  <div id="container"></div>
</div>
</template>

<script>
import G6 from '@antv/g6'
export default {
  name: 'G6Graph',
  data () {
    return {
      relNData: [
        {
          id: '0',
          label: '沽酒科技',
          type: 'company'
        },
        {
          id: '1',
          label: '董事1',
          type: 'director'
        },
        {
          id: '2',
          label: '经理1',
          type: 'director'
        },
        {
          id: '3',
          label: '职员1',
          type: 'staff'
        },
        {
          id: '4',
          label: '职员2',
          type: 'staff'
        },
        {
          id: '5',
          label: '职员3',
          type: 'staff'
        },
        {
          id: '6',
          label: '职员4',
          type: 'staff'
        },
        {
          id: '7',
          label: '职员5',
          type: 'staff'
        },
        {
          id: '8',
          label: '职员6',
          type: 'staff'
        },
        {
          id: '9',
          label: '职员7',
          type: 'staff'
        },
        {
          id: '10',
          label: '职员8',
          type: 'staff'
        }
      ],
      relEData: [
        {
          source: '0',
          target: '1',
          labels: '1',
          typest: 0
        },
        // 平行线
        // {
        //   source: '0',
        //   target: '1',
        //   labels: '1',
        //   typest: 0
        // },
        {
          source: '0',
          target: '2',
          labels: '2',
          typest: 1
        },
        {
          source: '0',
          target: '3',
          labels: '3',
          typest: 2
        },
        {
          source: '0',
          target: '4',
          labels: '4',
          typest: 2
        },
        {
          source: '0',
          target: '5',
          labels: '5',
          typest: 2
        },
        {
          source: '0',
          target: '6',
          labels: '6',
          typest: 2
        },
        {
          source: '0',
          target: '7',
          labels: '7',
          typest: 2
        },
        {
          source: '0',
          target: '8',
          labels: '8',
          typest: 2
        },
        {
          source: '0',
          target: '9',
          labels: '9',
          typest: 2
        },
        {
          source: '0',
          target: '10',
          labels: '10',
          typest: 2
        }
      ]
    }
  },
  mounted () {
    this.relationGS()
  },
  methods: {
    relationGS () {
      const data = {
        nodes: this.relNData,
        edges: this.relEData
      }
      // processParallelEdges 处理平行边
      G6.Util.processParallelEdges(data.edges)
      const tooltip = new G6.Tooltip({
        offsetX: 10,
        offsetY: 10,
        fixToNode: [1, 0.5],
        // 允许出现 tooltip 的 item 类型
        itemTypes: ['node'],
        // custom the tooltip's content
        // 自定义 tooltip 内容
        getContent: (e) => {
          const outDiv = document.createElement('div')
          outDiv.style.width = 'fit-content'
          outDiv.style.height = 'fit-content'
          const model = e.item.getModel()
          if (e.item.getType() === 'node') {
            outDiv.innerHTML = `${model.label}`
          }
          return outDiv
        }
      })
      const container = document.getElementById('container')
      const width = container.scrollWidth
      const height = container.scrollHeight || 800

      this.graph = new G6.Graph({
        container: 'container',
        width,
        height,
        plugins: [tooltip],
        modes: {
          default: [
            'drag-node',
            {
              type: 'drag-canvas',
              enableOptimize: true // enable the optimize to hide the shapes beside nodes' keyShape
            },
            {
              type: 'activate-relations',
              activeState: 'actives',
              inactiveState: 'inactives',
              resetSelected: false
            },
            {
              type: 'zoom-canvas'
            }
          ]
        },
        layout: {
          type: 'force',
          preventOverlap: true,
          linkDistance: 300,
          nodeStrength: 30,
          nodeSpacing: 40,
          // edgeStrength: 0.7,
          nodeSize: 46
        },
        defaultNode: {
          type: 'circle',
          size: 88,
          style: {
            cursor: 'grab'
          },
          labelCfg: {
            style: {
              fill: '#fff',
              positions: 'center',
              fixSize: true,
              fontSize: 14
            }
          }
        },
        defaultEdge: {
          style: {
            endArrow: true,
            lineWidth: 1
          },
          labelCfg: {
            autoRotate: true,
            style: {
              fontSize: 14
            }
          }
        },
        // 当前节点的多状态样式
        nodeStateStyles: {
          actives: {
            opacity: 1,
            lineWidth: 0
          },
          inactives: {
            opacity: 0.2,
            lineWidth: 0
          }
        },
        edgeStateStyles: {
          actives: {
            opacity: 1
          },
          inactives: {
            opacity: 0.2
          }
        }
      })

      data.nodes.forEach((node) => {
        node.style = {
          fill: node.type === 'company'
            ? '#EE5555'
            : node.type === 'director'
              ? '#0F8CFF'
              : '#FFC510',
          stroke: node.type === 'company'
            ? '#EE5555'
            : node.type === 'director'
              ? '#0F8CFF'
              : '#FFC510'
        }
      })
      data.edges.forEach((edge) => {
        edge.style = {
          stroke: edge.typest === 0
            ? '#EE5555'
            : edge.typest === 1
              ? '#0F8CFF'
              : '#FFC510'
        }
        edge.label = edge.typest === 0
          ? '董事'
          : edge.typest === 1
            ? '经理'
            : edge.typest === 2
              ? '职员'
              : null
        edge.labelCfg = {
          style: {
            fill: edge.typest === 0
              ? '#EE5555'
              : edge.typest === 1
                ? '#0F8CFF'
                : '#FFC510'
          }
        }
      })
      this.graph.data(data)
      this.graph.render()

      if (typeof window !== 'undefined') {
        window.onresize = () => {
          if (!this.graph || this.graph.get('destroyed')) return
          if (!container || !container.scrollWidth || !container.scrollHeight) return
          this.graph.changeSize(container.scrollWidth, container.scrollHeight)
        }
      }
    }
  }
}
</script>

<style scoped>

</style>
