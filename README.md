void  showSplash()
{
	int s=2000;
	printf("WELCOME TO HYPOTHETICAL MACHINE ASSEMBLER");
	printf("\n\n\tCREATED BY: NIDHI AND NIKITA");
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
				//printf("\nEnter Mnemonic Length: ");
				//scanf("%d",&l);
				l=3;

				fprintf(fp,"\n%s\t%d\t%d",m,c,l);
				fclose(fp);
		}

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
  void sleep(unsigned int m)

{
 clock_t goal = m+clock();
 while(goal>clock());
}

  
