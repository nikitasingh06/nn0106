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

  
