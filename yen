#include <fstream>
#include <queue>
#include <vector>
#include <algorithm>
#include <cstring>
#include <map>

using namespace std;
#define Inf 0x3f3f3f3f

ifstream cin("chromosome.in");
ofstream cout("chromosome.out");

/*

Algoritmi folositi in rezolvare :

Yen k shortesth paths
Dijkstra 

https://en.wikipedia.org/wiki/Yen%27s_algorithm
https://juliagraphs.org/Graphs.jl/stable/advanced/experimental/

*/

using pipvv = pair<int,pair<vector<int>,vector<int> > >; 
/// Retinem 
using pi = pair<int,int>;

int n,m,k,u,v;
vector<vector<pair<int,int>> > Graph;
vector<pipvv> YenK;
priority_queue< pipvv, vector<pipvv> , greater<pipvv> > pq;
vector<int> rootpath, spurPath, totalPath, CostCurent, CostRadacina, CostSpur;
map<pair<int,int>,bool> DeleteEdge;
map<int,bool> DeleteNode;
map<vector<int>,bool> InQueue;

bool colexicografic(pipvv a,pipvv b){

    if(a.first != b.first)
        return a.first < b.first; /// Dupa cost mai mic

    else{
        vector<int> v1,v2;
        v1 = a.second.first;
        v2 = b.second.first;
        
        /// Sortarea colexicografica 
        reverse(v1.begin(),v1.end());
        reverse(v2.begin(),v2.end());

        return v1 < v2;
    }
}

/*

    Parcurgem pe vectorul de tati pentru a reconstritui traseul

*/
void getpath(vector<int>& Drum,vector<int> t,int& final){

    if(t[final] != Inf)
        getpath(Drum,t,t[final]);
    
    Drum.push_back(final);

}
/*

    Calculam cu Dijkstra drumul cel mai scurt in graful G, transmis ca parametru.


*/
pipvv dijkstra(vector<vector<pair<int,int> > >& G,int start,int final){

    vector<int> d(n + 1,Inf),t(n + 1,Inf);
    vector<int> Drum;
    
    d[start] = 0;
    priority_queue<pi , vector<pi> , greater<pi> > q;
    q.push({0,start});
    while(!q.empty()){

        int x = q.top().second;
        q.pop();
        for(auto& i : G[x]){

            if(d[i.first] >= d[x] + i.second){
                d[i.first] = d[x] + i.second;
                
                    t[i.first] = x;

                q.push({d[i.first],i.first});
            }
        }
    }
    getpath(Drum,t,final);
    return make_pair(d[final],make_pair(Drum,d)); 
    /*
    Returnam costul drumului de la nodul start la nodul final
    Returnam Drumul, mai exact toate nodurile ce apartin acestui drum
    Returnam costul pentru a ajunge din nodul start in fiecare nod din graful G
    */

}
int main(){

    cin >> n >> m >> k >> u >> v;
    Graph.resize(n + 1);

    for(int i = 1 ; i <= m ; ++i){

        int x,y,z;
        cin >> x >> y >> z;
        Graph[x].push_back({y,z});

    }
    YenK.push_back(dijkstra(Graph,u,v)); 
    /// Coada tine in ordine maxim K drumuri de cost minim
    /// Primul drum este cel mai ieftin, mai exact cel gasit de algoritmul
    /// lui Dijkstra

    for(int i = 1 ; i < k ; ++i){ /// Incercam sa gasim k - 1 drumuri minime;

        for(int j = 0 ; j <= YenK[i - 1].second.first.size() - 2 ; ++j){ 
            /*
            Din drumul anterior gasit, luam cate un nod si il eliminam pe el, impreuna 
            cu muchiile asemanatoare cu cele din drumul anterior
            De exemplu, daca in drumul principal avem ca si drum minim
            1 2 3 4 8 6 (6 nod de final), spurnode va fi 1 iar in continuare 
            vom elimina muchia 1-2 
            */
            
            int spurnode = YenK[i - 1].second.first.at(j); 

            for(int ind = 0; ind <= j; ++ind)
                rootpath.push_back(YenK[i - 1].second.first.at(ind)); 
            /*
            Drumul de la nodul de inceput pana la spurnode(nodul ales)
            in exemplu, rootpath contine doar 1 deoarece deoarece 
            ind = 0 , j = 0 => ind == j si se adauga doar nodul 1 la pasul 1;

            */
           
            CostRadacina = YenK[i - 1].second.second; /*
            
            Retinem costul de la nodul de start la toate celelalte nodurile din graful
            initial.

            */
            DeleteEdge.clear();
            DeleteNode.clear(); 
            /* Map-urile acestea sunt folosite pentru a "simula" stergerea 
            unor noduri si muchii din graful curent, pentru ca drumul nou gasit, sa nu 
            coincida cu cel initial sau cu alte drumuri calculate la pasi anteriori.

            */ 

            for(auto& caut : YenK){

                /*
                Cautam in toate drumurile anterioare muchii identice pe care sa le stergem
                */

                vector<int> aux = caut.second.first;
                vector<int> AltDrum;

                if(aux.size() < rootpath.size())
                    continue;

                for(int ind = 0 ; ind <= j ; ++ind)
                    AltDrum.push_back(aux.at(ind)); 

                if(rootpath == AltDrum)
                    DeleteEdge[{aux.at(j),aux.at(j + 1)}] = true;
                
            }
            /// Orice nod anterior celui ales ca si "spurnode" se sterge si el
            for(int ind = 0; ind < j; ++ind)
                DeleteNode[rootpath.at(ind)] = true; 
            
            vector<vector<pair<int,int> > > NewGraph;

            NewGraph.resize(n + 1);

            for(int ii = 1 ; ii <= n ; ++ii){

                if(DeleteNode[ii] == true) 
                    continue;

                for(auto jj : Graph[ii]){
                    if(DeleteEdge[{ii,jj.first}] || DeleteNode[jj.first])
                        continue;
                    /// Daca muchia sau unul dintre nodurile curente nu este sters
                    /// Il adaugam in noul graf.
                    NewGraph[ii].push_back(jj);

                }
            }
            /// Calculam cu dijkstra un drum minim in graful NewGraph
            pipvv auxiliar = dijkstra(NewGraph,spurnode,v); 

            spurPath = auxiliar.second.first; /// Drumul de la spurnode pana la "final";

            totalPath = rootpath; /// Contine deocamdata nodurile pana la spurnode
            CostSpur = CostRadacina;
            CostCurent = auxiliar.second.second; /// Costul de la spurnode pana toate nodurile

            for(int ii = 1 ; ii <= n ; ++ii)
                if(DeleteNode[ii] == false && CostCurent[ii] != Inf)
                    CostSpur[ii] = CostRadacina[spurnode] + CostCurent[ii];
                    /// Costul de la spurnode la nodurile din graf
        
             for(auto &nod : spurPath)
                if(nod != spurnode)
                    totalPath.emplace_back(nod); /// Recreem drumul 
                
            int totalCost = CostRadacina[spurnode] + auxiliar.first;
            /// Costul pana la spurnode din nodul start + costul de la spurnode la final.
            /// Daca nu avem deja drumul in priorityqueue si am ajuns la final (cost != inf)
            if(!InQueue[totalPath] && auxiliar.first != Inf)
                InQueue[totalPath] = true, pq.push(make_pair(totalCost,make_pair(totalPath,CostSpur)));
                /// Bagam in priorityqueue unde sunt sortate

            rootpath.clear(), spurPath.clear(), totalPath.clear();
            for(auto &it : NewGraph)
                it.clear();

            NewGraph.clear();

           
            /// Curatam variabilele.
            
            }
            if(pq.empty())
                break;

            YenK.push_back(pq.top()); /// Adaugam cel mai "bun" drum gasit
            InQueue[pq.top().second.first] = false;  /// Il stergem din coada
            pq.pop();

    }
    sort(YenK.begin(),YenK.end(),colexicografic); /// Sortare pentru 100p

    cout << YenK.size() << '\n';/// Cate am gasit
    for (auto it : YenK){

        cout << it.first << ' ' << it.second.first.size() << '\n'; /// Cost si Nr Noduri
        for(auto jj : it.second.first)
            cout << jj << ' '; /// Nodurile

        cout << '\n';
    }
    
    


    return 0;
}
