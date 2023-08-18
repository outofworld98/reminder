## 1. Apache POI **
-  기존 O&M 보고서에서 사용하돈 Excel Template 사용 라이브러리
- 단점 :
  - pdf로 저장하는 기능이 존재하지 않음
  - 여러개의 excel을 하나의 단일 excel로 merge하는 기능이 존재하지 않음 (개별로 sheet의 row를 저장하는 기능만 존재함)
   https://gist.github.com/davidsommer/7805574
```
HSSFWorkbook
XSSFWorkbook
```

## 2. Aspose.cell  **
- 기능
  - 여러개의 excel을 하나의 excel로 merge하는 기능이 존재함
  - pdf를 포함한 여러 종 류의 파일로 저장하는 기능이 존재함
  - 참고 블로그 : https://blog.conholdate.com/total/combine-multiple-excel-files-into-one-using-java/

- 데모 (파일: https://www.pivotaltracker.com/story/show/184366954/comments/235330484): https://products.aspose.app/cells/merger  
- 개발자 문서 : https://docs.aspose.com/cells/java/#asposecells-for-java-resources
- API 문서 :https://reference.aspose.com/cells/java/com.aspose.cells
- github 자료 : https://github.com/aspose-cells/Aspose.Cells-for-Java/



## 구현 고려사항
 1. 보고서 챠트를 템플릿으로 구현된 상태에서 실제 데이터를 연계한 경우, 동적으로 실제 데이터 시트와 보고서 시트를 복사하여 여려개를 만드면
보고서 시트의 링크가 개별적으로 만들기가 쉽지 않다. 그리하여, workbook을 여러개 만들어 하나의 sheet로 통합하는 구성을 찾아보도록 함.
--> Aspeose.cell 에서 제공을 하지만, 최신 버전이 23.1 이고 현재 버전은 8.21.만 gradle 로 제공하므로, 많은 확인이 필요함.

2. 데이터와 보고서는 하나의 Sheet로 구성하여,  보고서는 인쇄영역으로 만들고, 데이터가 존재하는 Row의 height 는 0.1로 하여 화면에 보이지 않도록 함. (참고: height 가 0 이면 데이터가 사라지므로, 0.1로 설정함)
 -->  Apache poi로 cloneSheet(int sheetNum, String newName),  setHeightInPoints(float height); 함수 사용  
