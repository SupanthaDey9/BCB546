#BCB546_Spring

#UNIX Assignment by Supantha 

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
here is my snippet of code used for data inspection
   du -h fang_et_al_genotypes.txt
   echo "Lines Words Characters File"
   wc fang_et_al_genotypes.txt 
   awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt 
    
By inspecting this file I learned that:

1. The file size is 7Mb
2. There are 2783 Lines, 2744038 Words, and 2744038 Characters are present.
2. There are 986 columns in the file.


###Attributes of `snp_position.txt`

```
here is my snippet of code used for data inspection
```(head -n 3; tail -n 3) < snp_position.txt
    du -h snp_position.txt
    echo "Lines Words Characters File"
    wc snp_position.txt
    
By inspecting this file I learned that:

1. These following information are available: SNP_ID,cdv_marker_id,Chromosome_Position,alt_pos,mult_positions,amplicon,cdv_map_feature.name,gene,candidate/random,Genaissance_daa_id,Sequenom_daa_id,count_amplicons,count_cmf,count_gene. We also got to know about the head and tail of the file.

2. The file size is 49kb, 984 Lines, 13198 Words, and 82763 Characters are present.
 
3. There are 15 columns in the file.

##Data Processing

###Maize Data

```
here is my snippet of code used for data processing along with the brief description of what this code does.
   
#For both Data

``` head -n 1 fang_et_al_genotypes.txt > Maize_genotypes.txt
    head -n 1 fang_et_al_genotypes.txt > Teosinte_genotypes.txt
     grep -E "(ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt >> Maize_genotypes.txt
     grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt >> Teosinte_genotypes.txt'''
     
This take the header from the fang file, and then grep -E command takes the maize and teosinte data from the fang genotype and create new file.     

    '''awk -f transpose.awk Maize_genotypes.txt > transposed_Maize_genotypes.txt
     awk -f transpose.awk Teosinte_genotypes.txt > transposed_Teosinte_genotypes.txt
     wc -l transposed_Maize_genotypes.txt transposed_Teosinte_genotypes.txt'''
     
This part transposes the Maize and Teosinte data by calling the transpose.awk script which was already created. Also, wc-l was used to be sure that both files have the same amount of lines.

###Maize Data

      '''nano transposed_Maize_genotypes.txt
     { head -n 1 transposed_Maize_genotypes.txt; tail -n +4 transposed_Maize_genotypes.txt | sort -k1,1; } > sorted_transposed_Maize_genotypes.txt
      cut -f 1,3,4 snp_position.txt > 3column_snp_position.txt
      nano 3column_snp_position.txt 
      { head -n 1 3column_snp_position.txt; tail -n +2 3column_snp_position.txt | sort -k1,1; } > sorted_3column_snp_position.txt'''
      
  We analyzed the data with nano and figured out the sorting. We kept the heading as it is, and sorted based on the first column from the fourth line. We did it for both the snp_position
  and  Maize_genotypes file. Also, only 3 columns of snp_txt file was obtained as required by the question (id, chromosome and position).
       
       '''join -1 1 -2 1 -a 1 --head sorted_3column_snp_position.txt sorted_transposed_Maize_genotypes.txt  > Maize_joined.txt 
         { head -n 1 Maize_joined.txt; tail -n +2 Maize_joined.txt | sort -k3,3n; } > sorted_Maize_joined.txt
            for ((i=1; i<=10; i++)); 
               do 
                 awk -v i="$i" '$2 == i' sorted_Maize_joined.txt > "Maize_Chromosome_ascending_$i.txt"; 
               done'''
     Now, finally the snp and fang files are joined, and then sorted based on their position. Since we have to create 10 files, we make a for loop for the 10 distinct chromosome positions, which
     results in 10 outputs with different chromosome names and position ascending. awk -v is necessary when we use a variable, in this case i.
       
       
          '''{ head -n 1 Maize_joined.txt; tail -n +2 Maize_joined.txt | sed 's/?/-/g' | sort -k3,3nr; } > sorted_reverse_Maize_joined.txt

               for ((i=1; i<=10; i++)); 
                  do 
                        awk -v i="$i" '$2 == i' sorted_reverse_Maize_joined.txt > "Maize_Chromosome_descending_$i.txt"; 
                  done'''
                  
     Same work as before, but this time sort -k3,3nr was used to sort the file in descending order based on their position. Additionally, sed 's/?/-/g' was used to replace ? symbol to - symbol.
             
     '''        awk '$2 == "unknown" || $3 == "unknown"' Maize_joined.txt > Maize_unknown.txt
                awk '$2 == "multiple" || $3 == "multiple"' Maize_joined.txt > Maize_multiple.txt  '''

     Here we find the unknown and multiple chromosomes and positions in the files, create new files with only this data.  '$2 == "unknown" || $3 == "unknown"'  and '$2 == "multiple" || $3 ==
     "multiple"' find these information in the second (chromosome) and third(position) columns.  


    ###Teosinte Data
     ''' { head -n 1 transposed_Teosinte_genotypes.txt; tail -n +4 transposed_Teosinte_genotypes.txt | sort -k1,1; } > sorted_transposed_Teosinte_genotypes.txt
            join -1 1 -2 1 -a 1 --header sorted_3column_snp_position.txt sorted_transposed_Teosinte_genotypes.txt > Teosinte_joined.txt
 
         { head -n 1 Teosinte_joined.txt; tail -n +2 Teosinte_joined.txt | sort -k3,3n; } > sorted_Teosinte_joined.txt
            for ((i=1; i<=10; i++)); do
              awk -v i="$i" '$2 == i' sorted_Teosinte_joined.txt > "Teosinte_Chromosome_ascending_$i.txt"
                done '''

         ''' { head -n 1 Teosinte_joined.txt; tail -n +2 Teosinte_joined.txt | sed 's/?/-/g' | sort -k3,3nr; } > sorted_reverse_Teosinte_joined.txt
                 for ((i=1; i<=10; i++)); do
                    awk -v i="$i" '$2 == i' sorted_reverse_Teosinte_joined.txt > "Teosinte_Chromosome_descending_$i.txt"
                       done

     awk '$2 == "unknown" || $3 == "unknown"' Teosinte_joined.txt > Teosinte_unknown.txt
     awk '$2 == "multiple" || $3 == "multiple"' Teosinte_joined.txt > Teosinte_multiple.txt'''

     Same type of commands and purpose as the Maize data with no exception except the Teosinte data.


