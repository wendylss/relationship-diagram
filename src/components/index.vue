<template>
  <div>
    <p>《红楼梦》家族人物关系推理系统</p>
    <p style="position: absolute; top: 60px; right: 30px;font-weight:normal;">{{message}}</p>
    <el-button type="info" @click="clear" style="margin: 5px 10px 0 0;float:right">清空</el-button>
    <el-button type="primary" @click="onSubmit" style="margin: 5px 10px 0 0;float:right">查询</el-button>
    <el-select v-model="value" placeholder="所有关系" style="width: 150px;margin: 5px 10px 0 0;float:right;display:block;" @change="changeSelect()">
      <el-option
        v-for="item in options"
        :key="item.value"
        :label="item.label"
        :value="item.value">
      </el-option>
    </el-select>
    <el-input v-model="input1" placeholder="请输入人物名称" style="width: 150px;margin: 5px 10px 0 0;float:right"></el-input>
    <el-input v-model="input2" placeholder="请输入人物名称" :disabled="inputName" style="width: 150px;margin: 5px 10px 0 0;float:right"></el-input>
  <div id="container">
  </div>
  <div>
    <ul>
      <li style="display:inline;margin-right: 20px;"><i class="point1">贾家宁国府</i></li>
      <li style="display:inline;margin-right: 20px;"><i class="point2">贾家荣国府</i></li>
      <li style="display:inline;margin-right: 20px;"><i class="point3">史家</i></li>
      <li style="display:inline;margin-right: 20px;"><i class="point4">王家</i></li>
      <li style="display:inline;margin-right: 20px;"><i class="point5">林家</i></li>
      <li style="display:inline;margin-right: 20px;"><i class="point6">薛家</i></li>
      <li style="display:inline"><i class="point7">其他</i></li>
    </ul>
  </div>
  </div>
</template>

<script>
import * as d3 from 'd3'
import dagreD3 from 'dagre-d3'

export default {
  data () {
    return {
      allD: '贾演,贾代化,父亲,贾家宁国府,贾家宁国府#贾代化,贾演,儿子,贾家宁国府,贾家宁国府#贾敬,贾代化,儿子,贾家宁国府,贾家宁国府#贾代化,贾敬,父亲,贾家宁国府,贾家宁国府#焦大,贾演,老奴,贾家宁国府,贾家宁国府#贾演,焦大,主人,贾家宁国府,贾家宁国府#贾珍,贾敬,儿子,贾家宁国府,贾家宁国府#贾敬,贾珍,父亲,贾家宁国府,贾家宁国府#尤氏,贾珍,妻,贾家宁国府,贾家宁国府#贾珍,尤氏,丈夫,贾家宁国府,贾家宁国府#佩凤,贾珍,妾,贾家宁国府,贾家宁国府#偕鸾,贾珍,妾,贾家宁国府,贾家宁国府#贾蓉,贾珍,儿子,贾家宁国府,贾家宁国府#贾珍,贾蓉,父亲,贾家宁国府,贾家宁国府#秦可卿,贾蓉,妻,贾家宁国府,贾家宁国府#贾蓉,秦可卿,丈夫,贾家宁国府,贾家宁国府#瑞珠,秦可卿,丫环,贾家宁国府,贾家宁国府#宝珠,秦可卿,丫环,贾家宁国府,贾家宁国府#入画,贾惜春,丫环,贾家宁国府,贾家宁国府#贾珍,贾惜春,父亲,贾家宁国府,贾家宁国府#贾惜春,贾珍,女儿,贾家宁国府,贾家宁国府#尤氏,贾惜春,母亲,贾家宁国府,贾家宁国府#贾惜春,尤氏,女儿,贾家宁国府,贾家宁国府#贾源,贾代善,父亲,贾家荣国府,贾家荣国府#贾演,贾源,兄弟,贾家宁国府,贾家荣国府#贾代善,贾源,儿子,贾家荣国府,贾家荣国府#贾母,贾代善,妻,史家,贾家荣国府#贾代善,贾母,丈夫,贾家荣国府,史家#贾代善,贾政,父亲,贾家荣国府,贾家荣国府#贾政,贾代善,儿子,贾家荣国府,贾家荣国府#贾代善,贾赦,父亲,贾家荣国府,贾家荣国府#贾赦,贾代善,儿子,贾家荣国府,贾家荣国府#贾赦,贾母,儿子,贾家荣国府,史家#贾母,贾赦,母亲,史家,贾家荣国府#邢夫人,贾赦,妻,贾家荣国府,贾家荣国府#贾赦,邢夫人,丈夫,贾家荣国府,贾家荣国府#邢夫人,贾母,大儿媳,贾家荣国府,史家#贾母,邢夫人,婆婆,史家,贾家荣国府#贾政,贾母,儿子,贾家荣国府,史家#王夫人,贾母,小儿媳,王家,史家#鸳鸯,贾母,丫头,贾家荣国府,史家#林如海,贾敏,丈夫,林家,贾家荣国府#贾敏,林如海,妻,贾家荣国府,林家#林如海,林黛玉,父亲,林家,林家#林黛玉,林如海,女儿,林家,林家#贾敏,林黛玉,母亲,贾家荣国府,林家#贾母,林黛玉,外祖母,史家,林家#林黛玉,贾母,外孙女,林家,史家#紫鹃,贾母,丫头,贾家荣国府,史家#雪雁,林黛玉,丫头,贾家荣国府,林家#王熙凤,邢夫人,儿媳,王家,贾家荣国府#邢夫人,王熙凤,婆婆,贾家荣国府,王家#秋桐,贾赦,丫环,贾家荣国府,贾家荣国府#秋桐,贾赦,妾,贾家荣国府,贾家荣国府#贾琏,贾赦,儿子,贾家荣国府,贾家荣国府#贾赦,贾琏,父亲,贾家荣国府,贾家荣国府#王熙凤,贾琏,妻,王家,贾家荣国府#贾琏,王熙凤,丈夫,贾家荣国府,王家#贾迎春,贾赦,女儿,贾家荣国府,贾家荣国府#贾赦,贾迎春,父亲,贾家荣国府,贾家荣国府#贾迎春,孙绍祖,妻,贾家荣国府,其他#巧姐,王熙凤,女儿,贾家荣国府,王家#巧姐,贾琏,女儿,贾家荣国府,贾家荣国府#巧姐,刘姥姥,被抚养,贾家荣国府,其他#贾琏,巧姐,父亲,贾家荣国府,贾家荣国府#王熙凤,巧姐,母亲,王家,贾家荣国府#王熙凤,王夫人,内侄女,王家,王家#尤二姐,尤氏继母,女儿,贾家宁国府,贾家宁国府#尤二姐,贾琏,二房,贾家宁国府,贾家荣国府#王夫人,贾政,妻,王家,贾家荣国府#贾政,王夫人,丈夫,贾家荣国府,王家#赵姨娘,贾政,妾,贾家荣国府,贾家荣国府#王夫人,薛姨妈,姐妹,王家,王家#薛姨妈,王夫人,姐妹,王家,王家#金钏,王夫人,大丫环,贾家荣国府,王家#彩云,王夫人,大丫头,贾家荣国府,王家#彩霞,王夫人,大丫头,贾家荣国府,王家#玉钏儿,金钏儿,妹妹,贾家荣国府,贾家荣国府#金钏儿,玉钏儿,姐姐,贾家荣国府,贾家荣国府#玉钏儿,王夫人,丫头,贾家荣国府,王家#贾珠,王夫人,儿子,贾家荣国府,王家#贾珠,贾政,儿子,贾家荣国府,贾家荣国府#王夫人,贾珠,母亲,王家,贾家荣国府#贾政,贾珠,父亲,贾家荣国府,贾家荣国府#贾珠,李纨,丈夫,贾家荣国府,贾家荣国府#李纨,贾珠,妻,贾家荣国府,贾家荣国府#贾兰,贾珠,儿子,贾家荣国府,贾家荣国府#贾兰,李纨,儿子,贾家荣国府,贾家荣国府#贾元春,贾政,女儿,贾家荣国府,贾家荣国府#贾政,贾元春,父亲,贾家荣国府,贾家荣国府#贾元春,王夫人,女儿,贾家荣国府,王家#王夫人,贾元春,母亲,王家,贾家荣国府#贾宝玉,贾政,儿子,贾家荣国府,贾家荣国府#贾政,贾宝玉,父亲,贾家荣国府,贾家荣国府#贾元春,贾宝玉,姐姐,贾家荣国府,贾家荣国府#贾宝玉,王夫人,儿子,贾家荣国府,王家#王夫人,贾宝玉,母亲,王家,贾家荣国府#贾宝玉,薛宝钗,丈夫,贾家荣国府,薛家#薛宝钗,贾宝玉,妻,薛家,贾家荣国府#薛宝钗,薛姨妈,女儿,薛家,王家#袭人,贾宝玉,丫环,贾家荣国府,贾家荣国府#麝月,贾宝玉,丫环,贾家荣国府,贾家荣国府#秋纹,贾宝玉,丫环,贾家荣国府,贾家荣国府#晴雯,贾宝玉,丫环,贾家荣国府,贾家荣国府#坠儿,贾宝玉,丫环,贾家荣国府,贾家荣国府#四儿,贾宝玉,丫环,贾家荣国府,贾家荣国府#柳五儿,芳官,好朋友,贾家荣国府,贾家荣国府#柳五儿,贾宝玉,丫环,贾家荣国府,贾家荣国府#茗烟,贾宝玉,小厮,贾家荣国府,贾家荣国府#贾环,贾政,儿子,贾家荣国府,贾家荣国府#贾政,贾环,父亲,贾家荣国府,贾家荣国府#贾环,赵姨娘,儿子,贾家荣国府,贾家荣国府#赵姨娘,贾环,母亲,贾家荣国府,贾家荣国府#贾探春,贾政,女儿,贾家荣国府,贾家荣国府#贾政,贾探春,父亲,贾家荣国府,贾家荣国府#贾探春,赵姨娘,女儿,贾家荣国府,贾家荣国府#赵姨娘,贾探春,母亲,贾家荣国府,贾家荣国府#贾蔷,贾珍,养子,贾家宁国府,贾家宁国府#金文翔,鸳鸯,哥哥,其他,贾家荣国府#金文翔,贾母,买办,其他,史家#金文翔家的,鸳鸯,嫂子,其他,贾家荣国府#周瑞家的,王夫人,陪房,其他,王家#王善保家的,邢夫人,陪房,其他,贾家荣国府#林之孝,林红玉,父,其他,其他#林之孝家的,林红玉,母,其他,其他#李嬷嬷,贾宝玉,乳母,其他,贾家荣国府#李嬷嬷,李贵,母,其他,其他#傻大姐,贾母,丫头,其他,史家#芳官,贾宝玉,丫环,贾家荣国府,贾家荣国府#龄官,贾蔷,相好,其他,贾家宁国府#藕官,药官,相好,其他,其他#藕官,林黛玉,丫环,其他,林家#赖嬷嬷,赖大,母亲,其他,贾家宁国府#赖尚荣,赖嬷嬷,孙子,其他,其他#吴贵,晴雯,姑舅哥哥,其他,贾家荣国府#薛蟠,薛姨妈,儿子,薛家,王家#薛姨妈,薛蟠,母亲,王家,薛家#薛蝌,薛姨妈,侄儿,薛家,王家#邢岫烟,薛蝌,妻,其他,薛家#薛宝琴,薛姨妈,侄女,薛家,王家#薛姨妈,薛宝琴,姑母,王家,薛家#薛宝琴,薛蝌,胞妹,薛家,薛家#夏金桂,薛蟠,妻,薛家,薛家#香菱,薛蟠,妾,其他,薛家#香菱,甄士隐,女儿,其他,其他#甄士隐,香菱,父亲,其他,其他#宝蟾,夏金桂,陪房丫头,其他,薛家#邢岫烟,邢忠,女儿,其他,其他#邢岫烟,邢夫人,侄女,其他,贾家荣国府#莺儿,薛宝钗,丫头,其他,薛家#刘姥姥,王狗儿,岳母,其他,其他#贾雨村,林黛玉,老师,其他,林家#娇杏,贾雨村,二夫人,其他,其他#封肃,甄士隐,岳父,其他,其他#甄宝玉,甄应嘉,儿子,其他,其他#妙玉,林黛玉,朋友,其他,林家#妙玉,贾宝玉,朋友,其他,贾家荣国府#柳湘莲,薛蟠,好兄弟,其他,薛家#蒋玉菡,贾宝玉,好友,其他,贾家荣国府#蒋玉菡,袭人,夫妻,其他,贾家荣国府#冷子兴,周瑞家的,女婿,其他,其他#冷子兴,贾雨村,好友,其他,其他#马道婆,贾宝玉,乾娘,其他,贾家荣国府#智能儿,秦钟,暧昧,其他,其他#尤三姐,尤氏继母,女儿,贾家宁国府,贾家宁国府#尤三姐,尤二姐,妹妹,贾家宁国府,贾家宁国府#尤三姐,尤二姐,妹妹,贾家宁国府,贾家宁国府#秦钟,秦邦业,儿子,其他,其他#秦邦业,秦钟,父亲,其他,其他#秦邦业,秦可卿,养父,其他,贾家宁国府#史湘云,贾宝玉,朋友,史家,贾家荣国府#史湘云,林黛玉,朋友,史家,林家#史湘云,薛宝钗,朋友,史家,薛家#史湘云,贾母,孙女,史家,史家#王子腾,王熙凤,伯父,王家,王家#贾宝玉,林黛玉,表兄妹,贾家荣国府,林家#林黛玉,贾宝玉,表兄妹,林家,贾家荣国府#李纨,贾宝玉,嫂子,贾家荣国府,贾家荣国府#贾珠,贾宝玉,哥哥,贾家荣国府,贾家荣国府#贾敏,贾母,女儿,贾家荣国府,史家#贾敏,贾政,兄妹,贾家荣国府,贾家荣国府#贾敏,贾赦,兄妹,贾家荣国府,贾家荣国府#贾政,贾赦,兄弟,贾家荣国府,贾家荣国府#贾宝玉,贾珠,弟弟,贾家荣国府,贾家荣国府#贾代善,贾宝玉,爷爷,贾家荣国府,贾家荣国府#贾宝玉,贾代善,孙子,贾家荣国府,贾家荣国府#贾母,贾宝玉,奶奶,史家,贾家荣国府#秦钟,贾宝玉,朋友,其他,贾家荣国府',
      nodes: [ ],
      edges: [ ],
      Arr: [ ],
      statusName: ['贾家宁国府', '贾家荣国府', '史家', '王家', '林家', '薛家', '其他'],
      status: ['JN', 'JR', 'S', 'W', 'L', 'X', 'else'],
      input1: '',
      input2: '',
      options: [{
        value: '1',
        label: '一代关系'
      }, {
        value: '2',
        label: '二代关系'
      }, {
        value: '3',
        label: '所有关系'
      }, {
        value: '4',
        label: '两者关系'
      }],
      value: '',
      inputName: true,
      message: '',
      success: '0'
    }
  },
  mounted () {
    this.Arr = this.allD.split('#')
    this.loadData()
    // this.init()
  },
  methods: {
    loadData () {
      this.nodes = []
      this.edges = []
      for (var i = 0; i < this.Arr.length; i++) {
        var temp = this.Arr[i].split(',')
        for (var j = 0; j < 2; j++) {
          temp[j + 3] = this.switch(temp[j + 3])
        }
        this.nodes.push({ name: temp[0], status: temp[3] })
        this.nodes.push({ name: temp[1], status: temp[4] })
        this.edges.push({ source: temp[0], target: temp[1], label: temp[2] })
      }
      var set = new Set(this.nodes)
      this.nodes = Array.from(set)
      this.init()
    },
    switch (name) {
      switch (name) {
        case '贾家宁国府':name = 0; break
        case '贾家荣国府':name = 1; break
        case '史家':name = 2; break
        case '王家':name = 3; break
        case '林家':name = 4; break
        case '薛家':name = 5; break
        case '其他':name = 6; break
      }
      return name
    },
    // 清楚页面数据
    clear () {
      this.setAllNodesStyle('stroke-width: 1px;')
      this.setAllEdgesStyle('stroke-width: 1px')
      this.message = ''
      this.input1 = ''
      this.input2 = ''
    },
    // 设置所有节点格式
    setAllNodesStyle (style) {
      this.graph.nodes().forEach((node) => {
        this.setNodeStyle(node, style)
      })
    },
    // 设置所有边格式
    setAllEdgesStyle (style) {
      this.graph.edges().forEach((edge) => {
        this.setEdgeStyle(edge, style)
      })
    },
    // 设置节点格式
    setNodeStyle (node, style) {
      this.graph.node(node).elem.style = style
    },
    // 设置边格式
    setEdgeStyle (edge, style) {
      this.graph.edge(edge).elem.style = style
    },
    // 绘制有向图
    init () {
      // 创建svg
      d3.select('#container')// 选择container容器
        .append('svg')// 添加svg
        .attr('id', 'svg-canvas')
        .append('g')
        // 初始化
      this.graph = new dagreD3.graphlib.Graph()
      this.graph.setGraph({
        rankdir: this.direction
      }).setDefaultEdgeLabel(function () { return {} })
      this.nodes.forEach((node) => {
        this.graph.setNode(node.name, {
          label: node.name,
          id: node.name,
          shape: 'circle',
          class: this.status[node.status]
        })
      })

      this.edges.forEach((edge) => {
        this.graph.setEdge(edge.source, edge.target, {
          label: edge.label,
          curve: d3.curveBasis
        })
      })
      // 画图
      const svg = d3.select('svg')
      const inner = svg.select('g')
      // 缩放
      var zoom = d3.zoom().on('zoom', function () {
        inner.attr('transform', d3.event.transform)
      })
      svg.call(zoom)
      // eslint-disable-next-line new-cap
      const render = new dagreD3.render()
      render(d3.select('svg').select('g'), this.graph)
      let max = d3.select('svg')._groups[0][0].clientWidth > d3.select('svg')._groups[0][0].clientHeight ? d3.select('svg')._groups[0][0].clientWidth : d3.select('svg')._groups[0][0].clientHeight
      var initialScale = max / 6000 // initialScale元素放大倍数，随着父元素宽高发生变化时改变初始渲染大小
      var tWidth = (d3.select('svg')._groups[0][0].clientWidth - this.graph.graph().width * initialScale) / 2 // 水平居中
      var tHeight = (d3.select('svg')._groups[0][0].clientHeight - this.graph.graph().height * initialScale) / 2 // 垂直居中
      d3.select('svg').call(zoom.transform, d3.zoomIdentity.translate(tWidth, tHeight).scale(initialScale)) // 元素水平垂直居中
    },
    changeSelect () {
      if (this.value === '4') {
        this.inputName = false
      } else {
        this.inputName = true
      }
    },
    onSubmit () {
      if (this.value === '3') {
        if (this.edges.length !== 188) {
          d3.select('svg').remove()
          this.loadData()
        }
      }
      if (this.value === '1' && this.input1 !== '') {
        this.nodes = []
        this.edges = []
        this.findRelation(this.input1)
        d3.select('svg').remove()
        this.init()
        this.setNodeStyle(this.input1, 'stroke-width: 6px;')
      }
      if (this.value === '2' && this.input1 !== '') {
        this.nodes = []
        this.edges = []
        this.findRelation(this.input1)
        var tempNodes = this.nodes
        this.nodes = []
        this.edges = []
        for (var i = 0; i < tempNodes.length; i++) {
          this.findRelation(tempNodes[i].name)
        }
        d3.select('svg').remove()
        this.init()
        this.setNodeStyle(this.input1, 'stroke-width: 8px;')
      }
      if (this.value === '4' && this.input1 !== '' && this.input2 !== '') {
        if (this.edges.length !== 188) {
          d3.select('svg').remove()
          this.loadData()
        }
        this.setNodeStyle(this.input1, 'stroke-width: 10px;')
        this.setNodeStyle(this.input2, 'stroke-width: 10px;')
        this.edges.forEach((edge) => {
          if ((edge.source === this.input2) && (edge.target === this.input1)) {
            this.success = 1
            this.message = this.input2 + '是' + this.input1 + '的' + edge.label
            this.setEdgeStyle({v: edge.source, w: edge.target}, 'stroke-width: 7px')
          }
        })
        if (this.success === '0') {
          this.bfs(this.input2, this.input1)
        }
      }
    },
    bfs (name1, name2) {
      var time = 0
      var nodeslinks = []
      this.edges.forEach((edge) => {
        if (edge.source === name1) {
          nodeslinks.push(edge.source + '#' + edge.target)
          this.setEdgeStyle({v: edge.source, w: edge.target}, 'stroke-width: 7px')
        }
      })
      console.log(nodeslinks)
      var flag = 0
      for (var i = 0; ; i++) {
        if (flag) break
        if (i === 7) this.message = '系统还在计算，两者可能无关系！'
        var length = nodeslinks.length
        for (var j = 0; j < length; j++) {
          if (flag) break
          if (nodeslinks[0] === '') {
            this.message = '两者没有关系！'
            flag = 1
            break
          }
          var temp = nodeslinks[0].split('#')
          for (let k = 0; k < this.edges.length; k++) {
            if (this.edges[k].source === temp[temp.length - 1]) {
              if (temp.includes(this.edges[k].target)) {
                continue
              }
              nodeslinks.push(nodeslinks[0] + '#' + this.edges[k].target)
              let _this = this
              setTimeout(function () {
                _this.setEdgeStyle({v: _this.edges[k].source, w: _this.edges[k].target}, 'stroke-width: 7px')
              }, 1500 * (i + 1))
              time = 1500 * (i + 1)
              if (this.edges[k].target === name2) {
                flag = 1
                break
              }
            }
          }
          // 删去第一个数组
          nodeslinks.splice(0, 1)
        }
        console.log(nodeslinks)
      }
      this.showTrack(nodeslinks, time)
    },
    showTrack (arr, time) {
      var _this = this
      setTimeout(function () {
        var temp = arr[arr.length - 1].split('#')
        _this.setAllEdgesStyle('stroke-width: 1px')
        for (var i = 0; i < temp.length; i++) {
          _this.edges.forEach((edge) => {
            if (edge.source === temp[i] && edge.target === temp[i + 1]) {
              _this.setEdgeStyle({v: edge.source, w: edge.target}, 'stroke-width: 7px')
              console.log(edge)
              _this.message = _this.message + ' ' + edge.source + '是' + edge.target + '的' + edge.label
            }
          })
        }
      }, time + 1500)
    },
    findRelation (name) {
      for (var i = 0; i < this.Arr.length; i++) {
        var temp = this.Arr[i].split(',')
        if (name === temp[0] || name === temp[1]) {
          for (var j = 0; j < 2; j++) {
            temp[j + 3] = this.switch(temp[j + 3])
          }
          this.nodes.push({name: temp[0], status: temp[3]})
          this.nodes.push({name: temp[1], status: temp[4]})
          this.edges.push({source: temp[0], target: temp[1], label: temp[2]})
        }
      }
    }
  }
}
</script>

<style>
  #container {
    margin-top: 10px;
  }
  svg {
    width: 100%;
    height: 450px
  }
  .label {
    fill: black;
  }
  .node.JN {
    fill: #e9ec5f;
  }
  .node.JR {
    fill: #72a5d3;
  }
  .node.S {
    fill: #bb89d3;
  }
  .node.W {
    fill: #a7b0b8;
  }
  .node.L {
    fill: #a26172;
  }
  .node.X {
    fill: #dc8c73;
  }
  .node.else {
    fill: #83dc6b;
  }
  .node circle{
    stroke: #0d0d0d;
  }
  .nodes .node {
    cursor: pointer;
  }
  .edgePath path {
    stroke: #000000;
    fill: #000000;
  }
  ul {
    margin: 0;
    padding: 0;
    text-align: center;
  }
  li i {
    font-size: 0.9em;
    font-style: normal;
    color: #828990;
  }
  li i:before {
    display: inline-block;
    content: "";
    width: 20px;
    height: 20px;
    border-radius: 10px;
    background-color: #ff4a3b;
    margin-right: 4px;
    position: relative;
    top: 4px;
  }
  .point1:before {
    background-color: #e9ec5f;
  }
  .point2:before {
    background-color: #72a5d3;
  }
  .point3:before {
    background-color: #bb89d3;
  }
  .point4:before {
    background-color: #a7b0b8;
  }
  .point5:before {
    background-color: #a26172;
  }
  .point6:before {
    background-color: #dc8c73;
  }
  .point7:before {
    background-color: #83dc6b;
  }
  p {
    font-weight:bold;
    margin-top: 5px;
    font-size: 21px;
    float: left;
  }
</style>
