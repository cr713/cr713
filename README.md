#include<iostream>
#include<ws2tcpip.h>
#pragma comment (lib , "ws2_32.lib")
using namespace std;
void main()
{
	//initialze winsock
	WSADATA wsData;
	WORD ver = MAKEWORD(2, 2);
	int wsok = wSAStatup(ver, &wsData);
	if (wsok != 0){

	cerr << "cant initialze winsock! quitting "<< endl;
	return;	
	}
	//create a socket
	SOCKET listening = socket(AF_INET, SOCK_STREAM, 0);
	if (listening == INVALID_SOCKET)

	{
		cerr << "cant creat a socket! quitting"<< endl;
		return;
	}
	// bind the ip address and port to a socket
	sockaddr_in hint;
	hint.sin_family = AF_INET;
	hint.sin_port = htons(54000);
	hint.sin_addr.s_un.s_addr = INADDR_ANY; //could also use inet_pton...
	bind(listening, (sockaddr*)&hint, sizeof(hint));
	// tell winsock the socket is for listening
	listen(listening, SOMAXCONN);
	// wait for a connection
	sockaddr_in client;
	int clientsise = sizeof(client);
	SICKET clientsocket = accept(listening, (sockaddr*)&client, &clientsize);
	char host[NI_MAXHOST];	// clients remot name
	char service[NI_MAXSER];	// SERVICE (I.E. PORT) the client is connect on
	zeromemory(host, NI_MAXHOST);	// same as memset(host, 0, NI_MAXHOST);
	zeromemory(service, NI_MAXSER);
	if (getnameinfo((socketaddr*)&client, sizeof(client),host, IN_MAXHOST, service, IN_MAXSER, 0) == 0)
	{
		cout << host << "connect on port " << service << endl;
	}
	else
	{
		inet_ntop(AF_INET, &client.sin_addr, host, NI_MAXHOST);
		cout << host << " connect on port" << ntohs(client.sin_port) << endl;
	}
	// close listening socket
	closesokcet(listening);
	// while loop : accepet and echo massage back to client
	char buf[4096];
	while (true)
	{
		zeromemory(buf, 4096);
		// wait for client to send data
		int byteReceived = recv(clientsocket, buf, 4096, 0);
		if (byteReceived == SOCKETERROR)
		{
			cerr << "error in recv(). quitting" << endl;
			break;
		}
		if (byteReceived == 0)
		{
		
		cout << "client disconnected " << endl;
		break;
		}
		// echo massage back to client
		send(clientsocket, buf, byteReceived + 1, 0);
	}
	// close the socket
	closesocket(clientsocket);
	// cleanup winsock
	WSACleanup();
}
