#include<iostream>
#include<fstream>
#include<iomanip>
#include<cstring>
#include<locale>
using namespace std;
struct socio{
	long cedula;
	char nombre[50];
	long long fondos;
	char tipo[10];
	bool activo; 
};
struct asociado{
	long ced_socio;
	long ced_asociado;
	char nombre[50];
	int edad;
	char sexo;
	bool activo;
};
struct consumo {
    long num_factura;
    long ced_socio;
    long ced_consumidor;
    char concepto[50];
    double valor;
    char fecha[15];
    char estado[15];
};
void archivos(){
	 ofstream archivo1("Socios.txt", ios::binary | ios::out);
    if (archivo1.fail()) {
        cout << "No se ha podido crear el archivo..." << endl;
        exit(0);
    }
    archivo1.close();
    ofstream archivo2("Asociados.txt", ios::binary | ios::out);
    if (archivo2.fail()) {
        cout << "No se ha podido crear el archivo..." << endl;
        exit(0);
    }
    archivo2.close();
    ofstream archivo3("Retirados.txt", ios::binary | ios::out);
    if (archivo3.fail()) {
        cout << "No se ha podido crear el archivo..." << endl;
        exit(0);
    }
    ofstream archivo4("Consumo.txt", ios::binary | ios::out | ios::app);
    if (archivo4.fail()) {
        cout << "No se ha podido crear el archivo..." << endl;
        exit(0);
    }
    archivo4.close();
}

int val_socio(long did, char* nombre, long long* fondos);
int val_asociado(long das, char* name);
void actualizar_fondos(long cedula, long long nuevos_fondos);
void afiliar();
void registrarasociado();
void registrar_consumo();
void pagar_factura();
void aumentarfondos();
void cancelarSuscripcion();
void consultas();
void consultarsocio();
void listapersonas();
void listasociosactivos();
void listasociosretirados();
int menuprim();
int menucons();
int main() {
	archivos();
	int opc;
    struct socio datos1;
    struct asociado datos2;
    do {
    	opc=menuprim();
        system("cls");
        switch (opc) {
            case 1:
                afiliar();
                break;
            case 2:
                registrarasociado();
                break;
            case 3:
                registrar_consumo();
                break;
            case 4:
                pagar_factura();
                break;
  			case 5:
                aumentarfondos();
                break;
			case 6:
                cancelarSuscripcion();
                break;
			case 7:
                consultas();
                break;			              
            case 8:
                cout << "Saliendo..." << endl;
                return 0;
                break;
            default:
                cout << "Opcion no valida. Intente nuevamente..." << endl;
        }
        system("pause");
        system("cls");
    } while (opc != 8);
}
int menuprim(){
	int opc;
	do{
	cout << "CLUB SOCIAL" << endl << endl;
        cout << "1. Afiliar un socio al club" << endl;
        cout << "2. Registrar Asociado " << endl;
        cout << "3. Registrar Consumo" << endl;
        cout << "4. Pagar una factura " << endl;
        cout << "5. Aumentar fondos de la cuenta de un socio  " << endl;
        cout << "6. Cancelar Suscripcion Socio" << endl;
        cout << "7. Consultas " << endl;
        cout << "8. Salir " << endl;
        cout << "Escoja una opcion: ";
        cin >> opc;
        }while(opc<1 || opc>8);
	return opc;
}

int val_socio(long did, char* nombre, long long* fondos) {
    socio datos1;
    ifstream archivo("Socios.txt", ios::binary | ios::in);
    if (archivo.fail()) {
        cout << "No se pudo leer el archivo..." << endl;
        return 0;
    }
    archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio));
    while (!archivo.eof()) {
        if (did == datos1.cedula) {
            strcpy(nombre, datos1.nombre);
            *fondos = datos1.fondos;
            archivo.close();
            return 1;
        }
        archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio));
    }
    archivo.close();
    return 0;
}

int val_asociado(long das, char* name){
	asociado datos2;
    ifstream archivo("Asociados.txt", ios::binary | ios::in);
    if (archivo.fail()) {
        cout << "No se pudo leer el archivo..." << endl;
        exit(0);
    }
    archivo.read(reinterpret_cast<char*>(&datos2), sizeof(asociado));
    while (!archivo.eof()) {
        if (das == datos2.ced_asociado) {
            strcpy(name, datos2.nombre);
            archivo.close();
            return 1;
        }
        archivo.read(reinterpret_cast<char*>(&datos2), sizeof(asociado));
    }
    archivo.close();
    return 0;
}
void afiliar(){
	int cent=0;
	socio datos1;
	int vips=0;
	do{
		system("cls");
		cout << "Ingrese su cedula: ";
		cin >> datos1.cedula;
		long long fondos;
		if (val_socio(datos1.cedula, datos1.nombre, &fondos) == 1) {
			cout << "Ese socio ya ha sido registrado" << endl;
		} 
		else{
			cout << "Nombre: ";
			fflush(stdin);
			gets(datos1.nombre);
			cout << "Fondos disponibles: $";
			cin >> datos1.fondos;
			datos1.activo = true;
			if(datos1.fondos>=50000){
				if(vips>=3){
					cout << "Los cupos VIPs ya estan llenos" << endl;
					strcpy(datos1.tipo,"REGULAR");
					cout << "Tipo de suscripcion: " << datos1.tipo << endl;
					datos1.fondos=datos1.fondos-50000;
					cout << "Fondos actuales: " << datos1.fondos << endl;
				}
				else{
					do {
				        cout << "Tipo de suscripcion (VIP/REGULAR): ";
				        cin >> datos1.tipo;
					        for(int i=0;i<10;i++){
					        datos1.tipo[i] = toupper(datos1.tipo[i]);
							}	
				        if (strcmp(datos1.tipo,"VIP")!=0 && strcmp(datos1.tipo,"REGULAR")!=0) {
				            cout << "Error. La suscripcion ingresada no es vÃƒÆ’Ã‚Â¡lida." << endl;
				        }
				        if(strcmp(datos1.tipo,"VIP")==0){
				        	vips++;
							datos1.fondos=datos1.fondos-100000;	
						}
						else if(strcmp(datos1.tipo,"REGULAR")==0){
							datos1.fondos=datos1.fondos-50000;
						}
						if(datos1.fondos>0){
							cout << "Fondos actuales: " << datos1.fondos << endl;	
						}
				    }while (strcmp(datos1.tipo,"VIP")!=0 && strcmp(datos1.tipo,"REGULAR")!=0);	
				}	
				if(datos1.fondos>=0){
					ofstream archivo("Socios.txt", ios::binary|ios::app);
				if(archivo.fail()){
				cout << "No se pudo escribir en el archivo..." << endl;
				return;
				}
				archivo.write(reinterpret_cast<char*>(&datos1), sizeof(socio));
				archivo.close();
				}
				else{
					cout << "Sus fondos no son suficientes para afiliarse al club" << endl;
				}
			}
			else{
				cout << "Sus fondos no son suficientes para afiliarse al club" << endl;
			}
		}
		cout << "Desea registrar un nuevo socio?   0. Si  Otro. No" << endl;
		cin >> cent;
	}while(cent==0);
}
void registrarasociado(){
	int cent=0;
	socio datos1;
	asociado datos2;
	do{
		ifstream archivo("Socios.txt", ios::binary|ios::in);
		if(archivo.fail()){
				cout << "No se pudo leer el archivo..." << endl;
				return;
		}
		archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio));	
		system("cls");
		cout << "Ingrese la cedula del socio: ";
		cin >> datos2.ced_socio;
		if(datos2.ced_socio==datos1.cedula){
			if(datos1.fondos>0){
			cout << "Ingrese su cedula: ";
			cin >> datos2.ced_asociado;
			if(datos2.ced_socio==datos2.ced_asociado){
				cout << "La cedula del socio es igual a la suya" << endl;
			}else{
				if(val_asociado(datos2.ced_asociado, datos2.nombre)==1){
				cout << "Este asociado ya ha sido registrado" << endl;	
				}
				else{
					cout << "Nombre: ";
					fflush(stdin);
					gets(datos2.nombre);
					cout << "Edad: ";
					cin >> datos2.edad;
					datos2.activo = true;
					do {
				        cout << "Ingrese Sexo (M: Masculino / F: Femenino): ";
				        cin >> datos2.sexo;
				        datos2.sexo = toupper(datos2.sexo);
				        if (datos2.sexo != 'M' && datos2.sexo != 'F') {
				            cout << "Error. El sexo ingresado no es vÃƒÆ’Ã‚Â¡lido." << endl;
				        }
				    }while (datos2.sexo != 'M' && datos2.sexo != 'F');
				}
				ofstream archivoa("Asociados.txt", ios::binary|ios::app);
				if(archivoa.fail()){
				cout << "No se pudo escribir en el archivo..." << endl;
				return;
				}
				archivoa.write(reinterpret_cast<char*>(&datos2), sizeof(asociado));
				archivoa.close();	
				}
			}
			else{
				cout << "Sus fondos no son suficientes para asociar a la persona al club" << endl;
			}	
		}
		else{
			cout << "Ese socio no ha sido registrado" << endl;
		}
		archivo.close();
		cout << "Desea registrar un nuevo asociado?   0. Si  Otro. No" << endl;
		cin >> cent;
		}while(cent==0); 
	}
	
void actualizar_fondos(long cedula, long long nuevos_fondos) {
    socio datos1;
    fstream archivo("Socios.txt", ios::binary | ios::in | ios::out);
    if (archivo.fail()) {
        cout << "No se pudo acceder al archivo..." << endl;
        return;
    }
    archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio));
    while (!archivo.fail()) {
        if (cedula == datos1.cedula) {
            datos1.fondos = nuevos_fondos;
            archivo.seekp(-sizeof(socio), ios::cur);
            archivo.write(reinterpret_cast<char*>(&datos1), sizeof(socio));
            archivo.close();
            return;
        }
        archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio));
    }
    archivo.close();
}

void registrar_consumo() {
    consumo nuevo_consumo;
    char nombre_socio[50];
    long long fondos_disponibles;

    cout << "Ingrese el numero de la factura: ";
    cin >> nuevo_consumo.num_factura;
    cout << "Ingrese la cedula del socio: ";
    cin >> nuevo_consumo.ced_socio;
    if (!val_socio(nuevo_consumo.ced_socio, nombre_socio, &fondos_disponibles)) {
        cout << "El socio no esta registrado." << endl;
        return;
    }

    cout << "Ingrese la cedula del consumidor: ";
    cin >> nuevo_consumo.ced_consumidor;
    cin.ignore();

    cout << "Ingrese el concepto: ";
    cin.getline(nuevo_consumo.concepto, 50);
    cout << "Ingrese el valor del consumo: $";
    cin >> nuevo_consumo.valor;
    cin.ignore();

    if (fondos_disponibles < nuevo_consumo.valor) {
        cout << "El socio no tiene fondos suficientes para este consumo." << endl;
        return;
    }

    cout << "Ingrese la fecha (DD/MM/AAAA): ";
    cin.getline(nuevo_consumo.fecha, 15);

    strcpy(nuevo_consumo.estado, "PENDIENTE");

    ofstream archivo("Consumo.txt", ios::binary | ios::app);
    if (archivo.fail()) {
        cout << "No se pudo escribir en el archivo..." << endl;
        return;
    }
    archivo.write(reinterpret_cast<char*>(&nuevo_consumo), sizeof(consumo));
    archivo.close();

    actualizar_fondos(nuevo_consumo.ced_socio, fondos_disponibles - nuevo_consumo.valor);
    cout << "Consumo registrado exitosamente." << endl;
}
void pagar_factura() {
    long num_factura;
    consumo datos_consumo;
    bool encontrado = false;

    cout << "Ingrese el numero de la factura: ";
    cin >> num_factura;

    if (num_factura <= 0) {
        cout << "El numero de la factura debe ser un valor positivo." << endl;
        return;
    }

    fstream archivo("Consumo.txt", ios::binary | ios::in | ios::out);
    if (archivo.fail()) {
        cout << "No se pudo acceder al archivo..." << endl;
        return;
    }

    archivo.read(reinterpret_cast<char*>(&datos_consumo), sizeof(consumo));
    while (!archivo.fail()) {
        if (num_factura == datos_consumo.num_factura) {
            if (strcmp(datos_consumo.estado, "CANCELADO") == 0) {
                cout << "La factura ya esta cancelada." << endl;
                archivo.close();
                return;
            }
            strcpy(datos_consumo.estado, "CANCELADO");
            archivo.seekp(-sizeof(consumo), ios::cur);
            archivo.write(reinterpret_cast<char*>(&datos_consumo), sizeof(consumo));
            encontrado = true;
            break;
        }
        archivo.read(reinterpret_cast<char*>(&datos_consumo), sizeof(consumo));
    }
    archivo.close();

    if (encontrado) {
        cout << "Factura pagada exitosamente." << endl;
    } else {
        cout << "Factura no encontrada." << endl;
    }
}

void aumentarfondos(){
	long cedula;
    long long monto_aumentar;
    socio datos1;
    bool encontrado = false;

    cout << "Ingrese la cedula del socio: ";
    cin >> cedula;

    fstream archivo("Socios.txt", ios::binary | ios::in | ios::out);
    if (archivo.fail()) {
        cout << "Error al abrir el archivo de socios." << endl;
        return;
    }

    archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio));
    while (!archivo.fail()) {
        if (datos1.cedula == cedula) {
            encontrado = true;
            cout << "Socio encontrado:" << endl;
            cout << "Nombre: " << datos1.nombre << endl;
            cout << "Tipo de SuscripciÃ³n: " << datos1.tipo << endl;
            cout << "Fondos actuales: $" << datos1.fondos << endl;

            long long limite_maximo = strcmp(datos1.tipo, "VIP") == 0 ? 5000000 : 1000000;

            if (datos1.fondos >= limite_maximo) {
                cout << "El socio ya tiene el mÃ¡ximo permitido para su tipo de suscripciÃ³n ($" << limite_maximo << ")." << endl;
                archivo.close();
                return;
            }

            cout << "Ingrese el monto a aumentar: $";
            cin >> monto_aumentar;

            if (monto_aumentar <= 0) {
                cout << "El monto a aumentar debe ser mayor que 0." << endl;
                archivo.close();
                return;
            }

            long long nuevos_fondos = datos1.fondos + monto_aumentar;

            if (nuevos_fondos > limite_maximo) {
                cout << "El monto supera el lÃ­mite permitido para este tipo de suscripciÃ³n." << endl;
                cout << "El mÃ¡ximo permitido es: $" << limite_maximo << endl;
                archivo.close();
                return;
            }

            datos1.fondos = nuevos_fondos;

            archivo.seekp(-sizeof(socio), ios::cur);
            archivo.write(reinterpret_cast<char*>(&datos1), sizeof(socio));
            cout << "Fondos actualizados exitosamente. Nuevos fondos: $" << datos1.fondos << endl;
            archivo.close();
            return;
        }
        archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio));
    }

    if (!encontrado) {
        cout << "No se encontro un socio con la cedula proporcionada." << endl;
    }

    archivo.close();
}
void cancelarSuscripcion() {
    long cedula;
    int opc;

    cout << "Que desea cancelar?  0. Socio  1. Asociado " << endl;
    cout << "Opcion: ";
    cin >> opc;

    switch (opc) {
        case 0: { // Cancelar suscripción de un socio
            cout << "Ingrese la cedula del socio a cancelar: ";
            cin >> cedula;

            ifstream archivoSocios("Socios.txt", ios::binary);
            ofstream archivoTemp("TempSocios.txt", ios::binary);
            ofstream archivoRetirados("Retirados.txt", ios::binary | ios::app);

            if (!archivoSocios || !archivoTemp || !archivoRetirados) {
                cout << "Error al abrir los archivos." << endl;
                return;
            }

            socio datos;
            bool encontrado = false;

            while (archivoSocios.read(reinterpret_cast<char*>(&datos), sizeof(socio))) {
                if (datos.cedula == cedula) {
                    encontrado = true;
                    datos.activo = false;
                    archivoRetirados.write(reinterpret_cast<char*>(&datos), sizeof(socio));
                } else {
                    archivoTemp.write(reinterpret_cast<char*>(&datos), sizeof(socio));
                }
            }

            archivoSocios.close();
            archivoTemp.close();
            archivoRetirados.close();

            remove("Socios.txt");
            rename("TempSocios.txt", "Socios.txt");

            if (encontrado) {
                cout << "Suscripción del socio cancelada exitosamente." << endl;
            } else {
                cout << "No se encontró un socio con la cédula proporcionada." << endl;
            }

            break;
        }
        case 1: { // Cancelar suscripción de un asociado
            cout << "Ingrese la cedula del asociado a cancelar: ";
            cin >> cedula;

            ifstream archivoAsociados("Asociados.txt", ios::binary);
            ofstream archivoTemp("TempAsociados.txt", ios::binary);
            ofstream archivoRetirados("Retirados.txt", ios::binary | ios::app);

            if (!archivoAsociados || !archivoTemp || !archivoRetirados) {
                cout << "Error al abrir los archivos." << endl;
                return;
            }

            asociado datos;
            bool encontrado = false;

            while (archivoAsociados.read(reinterpret_cast<char*>(&datos), sizeof(asociado))) {
                if (datos.ced_asociado == cedula) {
                    encontrado = true;
                    datos.activo = false;
                    archivoRetirados.write(reinterpret_cast<char*>(&datos), sizeof(asociado));
                } else {
                    archivoTemp.write(reinterpret_cast<char*>(&datos), sizeof(asociado));
                }
            }

            archivoAsociados.close();
            archivoTemp.close();
            archivoRetirados.close();

            remove("Asociados.txt");
            rename("TempAsociados.txt", "Asociados.txt");

            if (encontrado) {
                cout << "Suscripción del asociado cancelada exitosamente." << endl;
            } else {
                cout << "No se encontró un asociado con la cédula proporcionada." << endl;
            }

            break;
        }
        default:
            cout << "Opción no válida." << endl;
            break;
    }

    cout << "El proceso de cancelación ha finalizado." << endl;
}

void consultas(){
	int opc;
	do {
      opc=menucons();
        system("cls");
        switch (opc) {
            case 1:
                consultarsocio();
                break;
            case 2:
                listapersonas();
                break;
            case 3:
                listasociosactivos();
                break;
            case 4:
                listasociosretirados();
                break;
            case 5:
                cout << "Saliendo..." << endl;
                break;
            default:
                cout << "Opcion no valida. Intente nuevamente..." << endl;
        }
        system("pause");
        system("cls");
    } while (opc != 5);
}

int menucons(){
    int opc;
	  do{
	  cout << "Consultas" << endl << endl;
        cout << "1. Consultar un socio " << endl;
        cout << "2. Lista de personas autorizadas por un socio " << endl;
        cout << "3. Listado de Socios Activos  " << endl;
        cout << "4. Listado de Socios Retirados  " << endl;
        cout << "5. Salir " << endl;
        cout << "Escoja una opcion: ";
        cin >> opc;
     }while(opc<1 || opc>5);
	 return opc;
	
}
void consultarsocio() {
    ifstream archivo("Socios.txt", ios::binary | ios::in);
    if (!archivo) {
        cout << "Error al abrir el archivo de socios." << endl;
        return;
    }

    long cedulaBusqueda;
    cout << "Ingrese la cedula del socio a consultar: ";
    cin >> cedulaBusqueda;

    socio datos1;
    bool encontrado = false;

    while (archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio))) {
        if (datos1.cedula == cedulaBusqueda) {
            cout << "Informacion del socio:" << endl;
            cout << "Cedula: " << datos1.cedula << endl;
            cout << "Nombre: " << datos1.nombre << endl;
            cout << "Tipo de Suscripcion: " << datos1.tipo << endl;
            cout << "Fondos disponibles: " << datos1.fondos << endl;
            encontrado = true;
            break; // Salir del bucle si se encuentra el socio
        }
    }

    if (!encontrado) {
        cout << "No se encontrÃ³ un socio con la cÃ©dula ingresada." << endl;
    }

    archivo.close();
}

void listapersonas() {
    ifstream archivo("Asociados.txt", ios::binary | ios::in);
    if (!archivo) {
        cout << "Error al abrir el archivo de asociados." << endl;
        return;
    }

    long cedulaSocioBusqueda;
    cout << "Ingrese la cedula del socio: ";
    cin >> cedulaSocioBusqueda;

    asociado datos2;
    bool encontrado = false;

    cout << "Personas autorizadas por el socio con cedula " << cedulaSocioBusqueda << ":" << endl;

    while (archivo.read(reinterpret_cast<char*>(&datos2), sizeof(asociado))) {
        if (datos2.ced_socio == cedulaSocioBusqueda) {
            if(datos2.activo==true){
			cout << "Cedula: " << datos2.ced_asociado
                 << ", Nombre: " << datos2.nombre
                 << ", Edad: " << datos2.edad
                 << ", Sexo: " << datos2.sexo << endl;
            encontrado = true;	
			}
        }
    }

    if (!encontrado) {
        cout << "No se encontraron personas autorizadas para este socio." << endl;
    }

    archivo.close();
}

void listasociosactivos() {
    ifstream archivo("Socios.txt", ios::binary | ios::in);
    if (!archivo) {
        cout << "Error al abrir el archivo de socios." << endl;
        return;
    }

    socio datos1;
    cout << "Listado de socios activos:" << endl;

    while (archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio))) {
        cout << "Cedula: " << datos1.cedula
             << ", Nombre: " << datos1.nombre
             << ", Tipo: " << datos1.tipo
             << ", Fondos: " << datos1.fondos << endl;
    }

    archivo.close();
}

void listasociosretirados() {
    ifstream archivo("Retirados.txt", ios::binary | ios::in);
    if (!archivo) {
        cout << "Error al abrir el archivo de socios retirados." << endl;
        return;
    }

    ofstream archivoContable("SociosRetiradosContable.txt");
    if (!archivoContable) {
        cout << "Error al crear el archivo contable." << endl;
        archivo.close();
        return;
    }

    socio datos1;
    cout << "Listado de socios retirados:" << endl;

    while (archivo.read(reinterpret_cast<char*>(&datos1), sizeof(socio))) {
        cout << "Cedula: " << datos1.cedula
             << ", Nombre: " << datos1.nombre
             << ", Tipo: " << datos1.tipo
             << ", Fondos: " << datos1.fondos << endl;

        archivoContable << "Cedula: " << datos1.cedula
                        << ", Nombre: " << datos1.nombre
                        << ", Tipo: " << datos1.tipo
                        << ", Fondos: " << datos1.fondos << endl;
    }

    cout << "Archivo contable generado: SociosRetiradosContable.txt" << endl;

    archivo.close();
    archivoContable.close();
}
