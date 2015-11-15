int searchOptabForSymbol(char file[], char smnemonic[], stopcode* op)// searching optable for symbol
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
  int dupSymbol(char file[], char lbl[])// function for identifying duplicate symbol
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

  
