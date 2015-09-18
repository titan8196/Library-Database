# Library-Database
A fully functional library database model.
 #include<stdio.h>
 #include<stdlib.h>
 #include<string.h>
 
 #define SIZE 10
 
 typedef struct Lib_type
{
	int lib_acc_num;
	char book_name[SIZE];
	char author_name[SIZE];
	int ISBN_number;
	char publisher_name[SIZE];
	int book_price;
	int bf;
	struct Lib_type *left;
	struct Lib_type *right;
}library_db;

int height(library_db *node)
{
	int ret_val;
	if(node == NULL)
	{
		ret_val = -1;
	}
	else if((node->left == NULL) && (node->right == NULL))
	{
		ret_val = 0;
	}
	else
	{
		int left_ht,right_ht;
		left_ht = height(node->left);
		right_ht = height(node->right);
		if(left_ht > right_ht)
		{
			ret_val = left_ht + 1;
		}
		else
		{
			ret_val = right_ht + 1;
		}
	}
	return ret_val;
}

void B_factor(library_db *root)
{
	if(root!=NULL)
	{
		B_factor(root->left);
		root->bf = (height(root->left) - height(root->right));
		B_factor(root->right);
	}
}

library_db *rightRotate(library_db *p)
{
	library_db *ret_val = p;
	if(p != NULL)
	{
		library_db *temp;
		temp = p->left;
		p->left = temp->right;
		temp->right = p;
		ret_val = temp;
	}
	return ret_val;
}

library_db *leftRotate(library_db *p)
{
	library_db *ret_val = p;
	if(p != NULL)
	{
		library_db *temp;
		temp = p->right;
		p->right = temp->left;
		temp->left = p;
		ret_val = temp;
	}
	return ret_val;
}

library_db *insert(library_db *root,library_db *node)
{
	int flag = 0,bal_fact;
	if(root==NULL)
	{
		root = node;
	}
	else
	{
			if(node->lib_acc_num < root->lib_acc_num)
			{
				root->left = insert(root->left,node);
			}
			else if(node->lib_acc_num > root->lib_acc_num)
			{
				root->right = insert(root->right,node);
			}
			else
			{
				root->ISBN_number = node->ISBN_number;
				strcpy(root->book_name,node->book_name);
				strcpy(root->author_name,node->author_name);
				strcpy(root->publisher_name,node->publisher_name);
				printf("\n***Record updated successfully***\n");
				flag = 1;
			}
		B_factor(root);
		if( (root->bf > 1) && (node->lib_acc_num < root->left->lib_acc_num)) //left left case
		{
			root = rightRotate(root);			
		}
		if( (root->bf > 1) && (node->lib_acc_num > root->left->lib_acc_num)) // left right case
		{
			root->left = leftRotate(root->left);
			root = rightRotate(root);
		}
		if( (root->bf < -1) && (node->lib_acc_num > root->right->lib_acc_num)) // right right case
		{
			root = leftRotate(root);
		}
		if( (root->bf < -1) && (node->lib_acc_num < root->right->lib_acc_num)) // right left case
		{
			root->right = rightRotate(root->right);
			root = leftRotate(root);
		}
	}
	B_factor(root);
	return root;
}

library_db *deleteRecord(library_db *root , int lib_num)
{
	int bal_fact;
	library_db *r , *q , *temp;
	if(root == NULL)
	{
		printf("\n***Record not found***\n");
	}
	else if( lib_num < root->lib_acc_num)
	{
		root->left = deleteRecord(root->left,lib_num);
	}
	else if( lib_num > root->lib_acc_num)
	{
		root->right = deleteRecord(root->right,lib_num);
	}
	else
	{
		printf("\n***Record deleted successfully***\n");
		if(root->left == NULL)
		{
			temp = root->right;
			free(root);
			root = temp;
		}
		else if(root->right == NULL)
		{
			temp = root->left;
			free(root);
			root = temp;
		}
		else
		{
			if(root->left->right == NULL && root->left->right == NULL)
			{
				temp = root->left;
				temp->right = root->right;
				free(root);
				root = temp;
			}
			else
			{
			
				for( (r = q = root->left) ; q->right != NULL; )
				{
					r = q;
					q = q->right;
				}
				r->right = q->left;
				q->left = root->left;
				q->right = root->right;
				free(root);
				root = q;
			}
		}
		B_factor(root);
		if( (root->bf > 1) && (root->lib_acc_num < root->left->lib_acc_num)) //left left case
		{
			root = rightRotate(root);			
		}
		if( (root->bf > 1) && (root->lib_acc_num > root->left->lib_acc_num)) // left right case
		{
			root->left = leftRotate(root->left);
			root = rightRotate(root);
		}
		if( (root->bf < -1) && (root->lib_acc_num > root->right->lib_acc_num)) // right right case
		{
			root = leftRotate(root);
		}
		if( (root->bf < -1) && (root->lib_acc_num < root->right->lib_acc_num)) // right left case
		{
			root->right = rightRotate(root->right);
			root = leftRotate(root);
		}
	}
	B_factor(root);
	return root;
}


library_db *search(library_db *root,int lib_num)
{
	library_db *ret_val = root;
	if(root!=NULL)
	{
		if(lib_num < root->lib_acc_num)
		{
			ret_val = search(root->left,lib_num);
		}
		else if( lib_num > root->lib_acc_num)
		{
			ret_val = search(root->right,lib_num);
		}
		else
		{
			ret_val = root;
		}
	}
	return ret_val;
}

void rangeSearch(library_db *root,int a,int b)
{
	if(root != NULL )
	{
		rangeSearch(root->left,a,b);
		if((root->lib_acc_num >= a) && (root->lib_acc_num <= b))	
		{	
			printf("Library Access Number:\t%d\nBook Name:\t%s\nAuthor Name:\t%s\nPrice:\t%d\nISBN Number:\t%d\n",root->lib_acc_num,root->book_name,root->author_name,root->book_price,root->ISBN_number);
			printf("Publisher Name:\t%s\n\n",root->publisher_name);
			printf("\n\t****\n");
		}
		rangeSearch(root->right,a,b);
	}
}

int numRecords(library_db *root,int count)
{
	if(root != NULL)
	{
		count = numRecords(root->left,count);
		count++;
		count = numRecords(root->right,count);
	}
	return count;
}

void ftraverse(library_db *root,FILE* fp)
{
	if(root != NULL)
	{
		ftraverse(root->left,fp);
		fprintf(fp,"%d %s %s %s %d %d %d",root->lib_acc_num,root->book_name,root->author_name,root->publisher_name,root->book_price,root->ISBN_number,root->bf);
		fprintf(fp,"\n");
		ftraverse(root->right,fp);
	}
}

int traverse(library_db *root)
{
	int count;
	if(root != NULL)
	{
		traverse(root->left);
		printf("%d  %s  %s  %s  %d %d",root->lib_acc_num,root->book_name,root->author_name,root->publisher_name,root->book_price,root->ISBN_number);
		printf(" %d\n",root->bf);
		printf("\n\t****\n");
		count++;
		traverse(root->right);
	}
	return count;
}

library_db *makenode()
{
	library_db *new_node;
	new_node = (library_db *)malloc(sizeof(library_db));
	printf("Library Access Number:\t");
	scanf("%d",&new_node->lib_acc_num);
	printf("Book Name:\t");
	scanf("%s",&new_node->book_name);
	printf("Author Name:\t");
	scanf("%s",&new_node->author_name);
	printf("Publisher Name:\t");
	scanf("%s",&new_node->publisher_name);
	printf("Price:\t");
	scanf("%d",&new_node->book_price);
	printf("ISBN Number:\t");
	scanf("%d",&new_node->ISBN_number);
	new_node->left = new_node->right = NULL;
	return new_node;
}

void main()
{
	FILE *fp;
	char c[50];
	int choice,flag = 0,hght,num,lo,hi,num_rec;
	library_db *root=NULL,*found,*new_node,*ptr;
	fp=fopen("avl.txt","r");
	while(fgets(c,sizeof(c),fp)!=NULL)
	{
		new_node = (library_db *)malloc(sizeof(library_db));
		sscanf(c,"%d %s %s %s %d %d %d",&new_node->lib_acc_num,new_node->book_name,new_node->author_name,new_node->publisher_name,&new_node->book_price,&new_node->ISBN_number,&new_node->bf);
		new_node->left = NULL;
		new_node->right = NULL;
		root = insert(root,new_node);
	}
	fclose(fp);
	do
	{
		printf("1. Insert\n");
		printf("2. Delete\n");
		printf("3. Search\n");
		printf("4. Number of records\n");
		printf("5. Height\n");
		printf("6. Range Search\n");
		printf("7. Print Records\n");
		printf("8. Exit\n\n");
		scanf("%d",&choice);
		switch(choice)
		{
			case 1:
				root = insert(root,makenode());
				break;
			case 2:
				printf("Enter the Record to be deleted:\t");
				scanf("%d",&num);
				root = deleteRecord(root,num);
				//traverse(root);
				break;
			case 3:
				printf("Enter the Library Access Number of the record to be searched: \n");
				scanf("%d",&num);
				found = search(root,num);
				if(found == NULL)
				{
					printf("Record not found\n");
				}
				else
				{
					printf("Record Found \n");
					printf("Library Access Number:\t%d\nBook Name:\t%s\nAuthor Name:\t%s\nPrice:\t%d\nISBN Number:\t%d\n",found->lib_acc_num,found->book_name,found->author_name,found->book_price,found->ISBN_number);
					printf("Publisher Name:\t%s\n\n",found->publisher_name);
					printf("\n\n");
				}
				break;
			case 4:
				num_rec = 0;
				num_rec = numRecords(root,num_rec);
				printf("Number of Records:\t%d\n",num_rec);
				break;
			case 5:
				hght = height(root);
				printf("Height of the tree is : %d\n",hght);
				break;
			case 6:
				printf("Enter Range for Searching \n");
				printf("Lower:\t");
				scanf("%d",&lo);
				printf("Higher:\t");
				scanf("%d",&hi);
				rangeSearch(root,lo,hi);
				break;
			case 7:
				traverse(root);
				break;
			case 8:
				flag = 1;
				break;
		}
	}while(flag == 0);
	ptr=root;
	fp=fopen("avl.txt","w");
	ftraverse(root,fp);
	fclose(fp);
}
