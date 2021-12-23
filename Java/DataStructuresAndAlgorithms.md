package graph;

import Linear.Queue;
import priority.IndexMinPriorityQueue;

public class DijkstraSP {
    //索引代表顶点，值表示从顶点s到当前顶点的最短路径上的最后一条边
    private DirectedEdge[] edgeTo;
    //索引代表顶点，值表示从顶点s到当前顶点的最短路径的总权重
    private double[] distTo;
    //存放树中顶点与非树中顶点之间的有效横切边
    private IndexMinPriorityQueue<Double> pq;


    //根据一幅加权有向图G和顶点s，创建一个根结点为s的最短路径树对象
    public DijkstraSP(EdgeWeightedDigraph G,int s){
        //初始化edgeTo
        this.edgeTo = new DirectedEdge[G.V()];
        //初始化distTo
        this.distTo = new double[G.V()];
        for (int i = 0; i < distTo.length; i++) {
            distTo[i] = Double.POSITIVE_INFINITY;
        }
        //初始化pq
        this.pq = new IndexMinPriorityQueue<Double>(G.V());

        //找到图G中以顶点s为起点的最短路径树

        //默认让顶点s进入到最短路径树中
        distTo[s] = 0.0;
        pq.insert(s,0.0);;
        //遍历pq
        while(!pq.isEmpty()){
            relax(G,pq.delMin());
        }
    }
    //松弛图G中的顶点v
    private void relax(EdgeWeightedDigraph G,int v){
        //遍历顶点v的邻接表，获取到所有与顶点v相邻的边
        for (DirectedEdge edge : G.adj(v)) {
            //获取到该边的终点w
            int w = edge.to();

            //通过松弛技术，判断从起点s到顶点w的最短路径是否需要从顶点s先到达顶点v，然后再由顶点v到达顶点w
            if(distTo(v)+edge.weight()<distTo(w)){
                distTo[w] = distTo[v]+ edge.weight();
                edgeTo[w] = edge;

                //判断pq中是否已经存在顶点w，如果存在，则更新数据，如果不存在，则直接添加
                if(!pq.isEmpty()){
                    pq.changeItem(w,distTo(w));
                }else{
                    pq.insert(w,distTo(w));
                }

            }
        }
    }
    //获取从顶点s到顶点v的最短路径的总权重
    public double distTo(int v){
        return distTo[v];
    }

    //判断从顶点s到顶点v是否可达
    public boolean hasPathTo(int v){
        return distTo[v]<Double.POSITIVE_INFINITY;
    }
    //查询从起点s到顶点v的最短路径中所有的边
    public Queue<DirectedEdge> pathTo(int v){
        //判断从s到v是否可达，如果不可达，则返回null
        if(!hasPathTo(v)){
            return null;
        }
        //创建队列对象
        Queue<DirectedEdge> allEdges = new Queue<>();
        while(true){
            DirectedEdge e = edgeTo[v];
            if(e==null){
                break;
            }
            allEdges.enqueue(e);
            
            v = e.from();
        }
        return allEdges;
    }

}
