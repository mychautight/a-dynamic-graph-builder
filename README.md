#include "graph.h"

Graph::Graph(int v) {
    nVertices = v; //constructor outlines the adjectives, not the functionality
    adjList = new Vertex*[nVertices]; //pointer points to the memory location of the dynamic array, which stores Vertex* (pointers to vertexes)
    for (int i = 0; i < nVertices; i++) {
        adjList[i] = nullptr; //set all pointers within the index of dynamic array to nullptr so that no problems are created
    }
}

void Graph::addEdge(int u, int v) {

    // Step 1: Validate the input
    if (u < 0 || u >= nVertices || v < 0 || v >= nVertices) { //check both from and to vertices exist, or else there is from and/or nothing
        return; // Invalid vertices, stop function and goes to next thing code says to do in main function as main function calls each function (to avoid if and else statements)
    }

    // Step 2: Check if the edge already exists
    Vertex* temp = adjList[u];
    while (temp != nullptr) { //if u has no neighbours, skip while loop
        if (temp->v == v) { //have pointer temp point to int v (vertex) rather than the vertex's pointer
            //if the first neighbour is what we are looking for, then return (edge already exists)
            return; // Edge already exists
        }
        temp = temp->next; //temp pointer points to the next node in linked list (ex. temp that pointer to the vertex containing 4, now is set to point to 4)
    }

    // Step 3: Create a new Vertex node for the neighbor
    Vertex* newVertex = new Vertex{v, nullptr}; //create a pointer that points to a new created node, that node contains int v and nullptr (it has no neighbours)

    // Step 4: Insert the new node at the head of the list
    newVertex->next = adjList[u]; //assign value of pointer within node to be the beginning of the linked list
    adjList[u] = newVertex;  //location of newVertex is the adjacency list at index u, it still points to the new vertex
}

void Graph::removeEdge(int u, int v) {
//Must find the pointer based off the index in the dynamic array, and set the vertex pointer to null ptr
    Vertex* current = adjList[u];    // Start with the head of the list, current pointer points to the same place as pointer l; current points to the first one in the list
    Vertex* previous = nullptr;
    while (current != nullptr) {
        if (current->v == v) { //Check to see if the vertex is the same, to find the right node
            if (previous == nullptr) {
                // If the node to remove is the head, update head to the next node
                adjList[u] = current->next;
            } else {
                // If the node is not the head, bypass it
                previous->next = current->next; //set the previous node's pointer = the current node's pointer (points to next)
            }

            delete current; // Delete the node that current pointer points to
            return; // Exit the function after deleting the node -> return or break?
        }
        previous = current; //move on until the next one until we find the right vertex
        current = current->next;
    }
}

Vertex* Graph::getNeighbours(int u) {
    // Step 1: Validate the input
    if (u < 0 || u >= nVertices) {
        return nullptr; // Invalid vertex, return nullptr
    }
    // Step 2: Return the head of the adjacency list for the given vertex
    return adjList[u];

    /*
        Get the neighbours of a vertex.

        The argument is the vertex whose neighbours we seek.

        The return value is a linked list of the neighbours. Each
            neighbour is a vertex, i.e., an int.

        You can assume that the caller will not deallocate the returned
        linked list; so you safely simply return adjList[i], where
        i is the argument.
    */
}

Edge* Graph::getEdges() {
    // Step 1: Initialize the head of the edges linked list
    Edge* edgeListHead = nullptr;

    // Step 2: Traverse all vertices in the graph
    for (int u = 0; u < nVertices; u++) {
        Vertex* current = adjList[u];

        // Step 3: Traverse the adjacency list for the current vertex
        while (current != nullptr) {
            // Create a new Edge node for the current edge (u -> current->v)
            Edge* newEdge = new Edge{u, current->v, edgeListHead};

            // Insert the new edge at the front of the edge list
            edgeListHead = newEdge;

            // Move to the next neighbor
            current = current->next;
        }
    }

    // Step 4: Return the head of the edge list
    return edgeListHead;
}

bool Graph::isReachable(int u, int v)
{
    bool reach[nVertices];
    for(int i = 0; i < nVertices; i++) reach[i] = false;
    reach[u] = true;

    bool reachChanged = true;
    while(reachChanged)
    {
        reachChanged = false; // optimistically set it to false
        for(int i = 0; i < nVertices; i++)
        {
            // if reach[i] is true, then for each neighbour j of
            // vertex i, set reach[j] to true
            if(reach[i])
            {
                // Walk the adjacency list for i
                for(Vertex *tmp = adjList[i]; tmp != nullptr; tmp = tmp->next)
                {
                    if(!(reach[tmp->v])) reachChanged = true;
                    reach[tmp->v] = true;
                }
            }
        }
    }

    return reach[v];
}

UndirectedGraph::UndirectedGraph(int v) : Graph(v) {

}

void UndirectedGraph::addEdge(int u, int v) {
  if (u!=v) { //can only add edge if they are distinct vertices, avoid clutter
      Graph::addEdge(u, v);
      Graph::addEdge(v, u);
  }
}

void UndirectedGraph::removeEdge(int u, int v) {
    //If the function is redefined, it won't be able to access certain members (adjList and nVertices)
    //In undirected graph the direction doesn't matter, it is just about the connection. So we must add an edge from -> to and to-> from
    //In undirected graph you can just remove the edge, but since inheritance is involved the 2 different edges must be removed
    //don't need the if condition, because the edge is only added to two distinct vertices
    Graph::removeEdge(u, v);
    Graph::removeEdge(v, u);
}

//used chatgpt for debugging
