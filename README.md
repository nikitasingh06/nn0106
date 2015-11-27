void  showSplash()
{
	int s=2000;
	printf("\n\n\n\n\n\n");
	printf("\t\t\tWELCOME TO HYPOTHETICAL MACHINE ASSEMBLER");
	printf("\n\n\n\n\n\n");
	printf("\t\t\tDeveloped BY:");
	printf("\n\t\t\t\t\t NIDHI");
	printf("\n\t\t\t\t\t NIKITA");
	sleep(s);
}
char getChoice()
{
	char choice;


	printf("\n*************** MENU ***************\n");
	printf("\n 1 : Mnemonics Summary");
	printf("\n 2 : Add Mnemonics");
	printf("\n 3 : Execute Pass 1");
	printf("\n 4 : Show Intermediate Code Generated by Pass 1");
	printf("\n 5 : Show Symbol Table Generated by Pass 1");
	printf("\n 6 : Execute Pass 2");
	printf("\n 7 : Object Code Generated by Pass 2");
	printf("\n 8 : Exit");

	printf("\n************************************");
	printf("\n\nEnter your choice : ");
	choice = getche();

	printf("\n************************************");

	return choice;
}
int pass1(char inputFile[])
{
 char opcode[10],operand[10],label[10];
 stopcode op;
 int locctr,start,len;

 char sTable[20], opTable[20], interFile[20];
 FILE *fp1,*fp2,*fp3;


 strcpy(interFile, "I_");
 strcpy(sTable, "S_");

 strcat(interFile, inputFile);
 strcat(sTable, inputFile);
 strcpy(opTable, "OPTAB.dat");


 fp1=fopen(inputFile,"r");
 fp2 = fopen(sTable,"w");
 fp3=fopen(interFile,"w");

 fclose(fp2);
 fscanf(fp1,"%s%s%s",label,opcode,operand);
 if(strcmp(opcode,"START")==0)
  {
	start=atoi(operand);
	locctr=start;
	fprintf(fp3,"%s\t%s\t%s\n",label,opcode,operand);
	fscanf(fp1,"%s%s%s",label,opcode,operand);
  }
 else
  locctr=0;


 while(strcmp(opcode,"END")!=0)
  {
	fprintf(fp3,"%d",locctr);
	if(strcmp(label,"**")!=0)
	{
	  if(!dupSymbol(sTable,label))
			addSymbol(sTable,label,locctr);
	  else
	  {
		printf("\n\nERROR : Duplicate Symbol Encountered\n\n");
		return(0);
	  }
	}

	if( searchOptabForSymbol(opTable,opcode,&op))
	{
	  locctr= locctr + op.length;
	}
	if(strcmp(opcode,"WORD")==0)
	 locctr+=3;
	else if(strcmp(opcode,"RESW")==0)
	 locctr+=(3*(atoi(operand)));
	else if(strcmp(opcode,"RESB")==0)
	 locctr+=(atoi(operand));
	else if(strcmp(opcode,"BYTE")==0)
	 ++locctr;
	fprintf(fp3,"\t%s\t%s\t%s\n",label,opcode,operand);
	fscanf(fp1,"%s%s%s",label,opcode,operand);
  }
  fprintf(fp3,"%d\t%s\t%s\t%s\n",locctr,label,opcode,operand);
  fclose(fp3);
  //len=locctr-start;
  //printf("\nThe length of the program is %d.\n\n",len);
  return 1;
}
int pass2(char inputFile[])
{
	char a[10],ad[10],label[10],opcode[10],operand[10],trec[100],trectemp[100];
	int st,diff,i,address,add,trecstartadd,len,actual_len,finaddr,prevaddr,j=0,count=0;

 stopcode op;


 char sTable[20], opTable[20], interFile[20], objectFile[20],assblyFile[20],modr[100],modrt[30];
 FILE *fp0,*fp1,*fp2,*fp3,*fp4;


 strcpy(interFile, "I_");
 strcpy(sTable, "S_");

 strcpy(assblyFile, "A_");
 strcpy(objectFile, "O_");

 strcat(interFile, inputFile);
 strcat(sTable, inputFile);
 strcat(assblyFile, inputFile);
 strcat(objectFile, inputFile);


 strcpy(opTable, "OPTAB.dat");

 fp0=fopen(inputFile,"r");
 fp1=fopen(assblyFile,"w");
 fp2 = fopen(sTable,"r");
 fp3=fopen(interFile,"r");
 fp4=fopen(objectFile,"w");

 if(fp0==NULL)
 {
  printf("\n\nERROR : Source File does not exist");
  return 0;
 }

 if(fp2==NULL)
 {
  printf("\n\nERROR : Symbol Table does not exist");
  return 0;
 }
 if(fp3==NULL)
 {
  printf("\n\nERROR : Intermediate File does not exist");
  return 0;
 }

  fclose(fp0);
  fclose(fp2);
  fscanf(fp3,"%s%s%s",label,opcode,operand);

  address=0;

  fscanf(fp3,"%x%s%s%s",&address,label,opcode,operand);

  while(strcmp(opcode,"END")!=0)
  {
	prevaddr=address;
	fscanf(fp3,"%d%s%s%s",&address,label,opcode,operand);
  }
  finaddr=address;
  fclose(fp3);

  fp3=fopen(interFile,"r");

  fscanf(fp3,"%s%s%s",label,opcode,operand);
  if(strcmp(opcode,"START")==0)
  {
	fprintf(fp1,"\t%s\t%s\t%s\n",label,opcode,operand);
	fprintf(fp4,"H^%s^00%s",label,operand);

	fscanf(fp3,"%d%s%s%s",&address,label,opcode,operand);
	st=address;
	trecstartadd=address;
	diff=prevaddr-st;

	fprintf(fp4,"^%06d\n",finaddr-st);

  }

    strcpy(modr,"");
    strcpy(trec,"");

  while(strcmp(opcode,"END")!=0)
  {
	if(strcmp(opcode,"BYTE")==0)
	{
	 fprintf(fp1,"%04d\t%s\t%s\t%s\t\t",address,label,opcode,operand);
	 len=strlen(operand);
	 actual_len=len-3;

	 sprintf(trectemp,"^");
	 strcat(trec,trectemp);

	 for(i=2;i<(actual_len+2);i++)
	 {
	  itoa(operand[i],ad,16);
	  fprintf(fp1,"%s",ad);


	  sprintf(trectemp,"%s",ad);
	  count+=strlen(ad);

	 }

	 fprintf(fp1,"\n");
	}
	else if(strcmp(opcode,"WORD")==0)
	{

        fprintf(fp1,"%04d\t%s\t%s\t%s\t\t%06x\n",address,label,opcode,operand,atoi(operand));

        sprintf(trectemp,"^%06x",atoi(operand));
        strcat(trec,trectemp);

        count+=6;
	}
	else if((strcmp(opcode,"RESB")==0)||(strcmp(opcode,"RESW")==0))
	 fprintf(fp1,"%04d\t%s\t%s\t\t%s\n",address,label,opcode,operand);
	else
	{
		if( searchOptabForSymbol(opTable,opcode,&op))
		{
		  add = getSymbolValue(sTable,operand);
		  if(add == 0)
		  {
			  printf("\nSymbol not found : %s\n",operand);
			  getch();
		  }
		  else
		  {

			 if(strcmp(operand,"COPY")==0)
				fprintf(fp1,"%04d\t%s\t%s\t%s\t\t%d0000\n",address,label,opcode,operand,op.code);
			 else
			 {
			      sprintf(modrt,"\nM^%06d^04",address);
			      strcat(modr,modrt);
			      fprintf(fp1,"%04d\t%s\t%s\t%s\t\t%02d%04d\n",address,label,opcode,operand,op.code,add);
                  sprintf(trectemp,"^%02d%04d",op.code,add);
                  strcat(trec, trectemp);
                  count+=6;


				  if(count==60)
                  {

                    fprintf(fp4,"\nT^%06d^%x%s",trecstartadd,count/2,trec);
                    trecstartadd = trecstartadd + count/2;
                    strcpy(trec,"");
                    strcpy(trectemp,"");
                    count=0;
                  }

			 }
		  }
		}
	}
	fscanf(fp3,"%d%s%s%s",&address,label,opcode,operand);
  }

 if(count!=0)
 {

   fprintf(fp4,"\nT^%06d^%x%s",trecstartadd,count/2,trec);
 }

    fprintf(fp1,"%04d\t%s\t%s\t%s\n",address,label,opcode,operand);
    fprintf(fp4,"%s",modr);
    fprintf(fp4,"\nE^%06d",st);

  fclose(fp1);
  fclose(fp2);
  fclose(fp3);
  fclose(fp4);

  return 1;
}
void addMnemonics()
{
		FILE *fp;
		int c,l;
		char m[20];

		fp=fopen("OPTAB.DAT","a");

		if(fp==NULL)
		{
				printf("\nERROR: in adding Mnemonics");
		}
		else
		{
				printf("\nEnter Mnemonic Name: ");
				scanf("%s",m);
				printf("\nEnter Mnemonic Code: ");
				scanf("%d",&c);

				if(getNumberOfDigits(c)>2)
                {
                    printf("\nERROR : Max Length of code is two digits");
                    return;
                }
				//printf("\nEnter Mnemonic Length: ");
				//scanf("%d",&l);
				l=3;

				fprintf(fp,"\n%s\t%d\t%d",m,c,l);
				fclose(fp);
		}

}

// work only for positive numbers
int getNumberOfDigits (int i)
{
    return i > 0 ? (int) log10 ((double) i) + 1 : 1;
}



void displayMnemonics()
{
		FILE *fp;
		char m[20];
		int c,l;

		fp=fopen("OPTAB.DAT","r");

		if(fp==NULL)
		{
				printf("\nERROR: in reading Mnemonics");
		}
		else
		{
				printf("\n_____________________________________________\n");
				printf("\nMnemonics Supported");
				printf("\n_____________________________________________\n");
				printf("\n%15s%9s%9s","Name","Code","Length");
				printf("\n_____________________________________________\n");


				while((fscanf(fp,"%s%d%d",m,&c,&l))!=EOF)
				{
					printf("\n%15s%9d%9d",m,c,l);
				}
				fclose(fp);
				printf("\n_____________________________________________\n\n");
		}

}
void dispFile(char st[], char name[])
  {
		FILE *fp;
		char ch;

		fp=fopen(st,"r");

		if(fp==NULL)
		{
				printf("\nERROR: in reading %s file", name);
		}
		else
		{
				printf("\n_____________________________________________\n");
				printf("The contents of %s file :", name);
				printf("\n_____________________________________________\n");

				ch=fgetc(fp);
				while(ch!=EOF)
				{
					printf("%c",ch);
					ch=fgetc(fp);
				}
				fclose(fp);
				printf("\n_____________________________________________\n\n");
		}
  }

int searchOptabForSymbol(char file[], char smnemonic[], stopcode* op)
{
		FILE *fp;

		fp=fopen(file,"r");

		if(fp==NULL)
		{
				printf("\nERROR: in opening OpTable");
		}
		else
		{
				while(fscanf(fp,"%s\t%d\t%d",op->mnemonic,&op->code,&op->length)!=EOF)
				{
				 if(strcmp(op->mnemonic,smnemonic)==0)return 1;
				}
				fclose(fp);
		}
		return 0;

}
  int dupSymbol(char file[], char lbl[])
  {
		FILE *fp;
		char label[20];
		int loc;

		fp=fopen(file,"r");

		if(fp==NULL)
		{
				printf("\nERROR: in opening Symbol Table");
		}
		else
		{
				while(fscanf(fp,"%s\t%d",label,&loc)!=EOF)
				{
				 if(strcmp(label,lbl)==0)return 1;
				}
				fclose(fp);
		}
		return 0;
  }
int getSymbolValue(char file[], char lbl[])
  {
		FILE *fp;
		char label[20];
		int loc;

		fp=fopen(file,"r");

		if(fp==NULL)
		{
				printf("\nERROR: in opening Symbol Table");
		}
		else
		{
				while(fscanf(fp,"%s\t%d",label,&loc)!=EOF)
				{
				 if(strcmp(label,lbl)==0)return loc;
				}
				fclose(fp);
		}
		return 0;
  }
  void addSymbol(char st[], char label[], int loc)
	{
		FILE *fp;

		fp=fopen(st,"a");

		if(fp==NULL)
		{
				printf("\nERROR: in adding Symbol in Symbol Table");
		}
		else
		{
				fprintf(fp,"%s\t%04x\n",label,loc);
				fclose(fp);
		}
  }
  void sleep(unsigned int m)

{
 clock_t goal = m+clock();
 while(goal>clock());
}
void getHexValue(int n, char* hex, int l)
{
 int i,len;
 char *t = (char*)malloc(sizeof(char)*l);

 itoa(n,t,16);
 len = strlen(t);

 for(i=0;i<l-len-1;i++)
	hex[i]='0';
 hex[i]='\0';
 strcat(hex,t);
}
void fillZeros(int n, char* hex, int l)
{
 int i,len;
 char *t = (char*)malloc(sizeof(char)*l);

 itoa(n,t,10);
 len = strlen(t);

 for(i=0;i<l-len-1;i++)
	hex[i]='0';
 hex[i]='\0';
 strcat(hex,t);
}
int getStartAdd(char f[])
{
		FILE *fp;
        char temp[60],st[7],*p;
        int i,j;
		fp=fopen(f,"r");

		if(fp==NULL)
		{
				printf("\nERROR: in reading Start Location from Object File for Loader");
				return -1;
		}
		else
		{

                while(fgets(temp,59,fp))
                {
                    if(temp[0]=='H')
                    {
                        p=strstr(temp,"^");
                        p=strstr(p+1,"^");
                        p++;
                        i=0;
                        j=0;
                        while(p[i]!='^')st[j++]=p[i++];
                        p[i]='\0';
                        break;
                    }
                }
				fclose(fp);
				return strToHex(st);
		}
}
int strToHex(char s[])
{
    char *p;
    int uv=0;
    uv=strtoul(s, &p, 16);
    return uv;
}

void getTRecord(char f[],char trecord[])
{
		FILE *fp;
        char temp[72];
        int loc;
		fp=fopen(f,"r");

		if(fp==NULL)
		{
				printf("\nERROR: in reading Object File for Loader");
		}
		else
		{
                strcpy(trecord,"");
                while(fgets(temp,72,fp))
                {
                    if(temp[0]=='T')
                    {
                        strcat(trecord,temp);
                        loc = strcspn(trecord,"\n");
                        if(loc!=-1)
                            trecord[loc]='\0';
                    }
                }
				fclose(fp);
        }
}
void trecT0Str(char *trecord,char* str, int skip)
{
	char*p;
	int i=0,trecLen,m=0,count,		 tFound=0;

	while(trecord[i]!='\0')
	{
		if(trecord[i]=='T')
		{
		 tFound=1;
		 count=skip;
		 i++;
		 continue;
		}

		if(tFound && count!=0)
		{
			if(trecord[i]=='^')count--;
			if(count==0)
			{
			 tFound=0;
			}
			i++;
			continue;
		}
		if(trecord[i]!='^')
			str[m++]=trecord[i];
		i++;

	}
	str[m]='\0';
}
void loaderHeader()
{
    printf("\n--------------------------------------------------------------------------");
    printf("\n\t\t\t\tLoader Output");
	printf("\n--------------------------------------------------------------------------");
	printf("\nMemory Address\t\t             Contents");
	printf("\n--------------------------------------------------------------------------");
}
void loader(char s[], int locc)
{
	int i;
	// fill object code
	for(i=0;s[i]!='\0';i++)
	{
			if(i%32==0)
			{
				printf("\n%04x\t",locc);
				locc += 16;
			}
			if(i%8==0 )
			{
				printf("\t");
			}

		printf("%c",s[i]);

	}
	// fill remaining entries in line

	if(i%32!=0)
	{
		while(i%32!=0)
		{
			if(i%8==0 )
			{
				printf("\t");
			}

			printf("x");
			i++;
		}
	}
}

void loaderUpper(int locc)
{

	int i,j;
	// fill upper portion
	for(j=0;(j<locc && j<35 );j=j+16)
	{
		for(i=0;i<32;i++)
		{
			if(i%32==0)
			{
				printf("\n%04x\t",j);
			}
			if(i%8==0)
			{
				printf("\t");
			}
			printf("x");
		}
	}
	if(j<locc)
	{
		for(i=0;i<3;i++)
		{
			printf("\n----\t\t--------\t--------\t--------\t--------");
		}
	}

}

void loaderLower()
{

	int j;
	//fill last portion
	for(j=0;j<3;j++)
	{
			printf("\n----\t\t--------\t--------\t--------\t--------");
	}

}

  
