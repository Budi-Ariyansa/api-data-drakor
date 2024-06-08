from flask import Flask
from datetime import datetime
import psycopg2
import psycopg2.extras
import globalVar
import json

app = Flask(__name__)

def setResponse(response_code, response_description, **other_response):
    return json.dumps({
        "response_code": response_code,
        "response_description": str(response_description),
        "datetime": str(datetime.now().replace(microsecond=0))
    }) if not other_response else json.dumps({
        "response_code": response_code,
        "response_description": response_description,
        **other_response,
        "datetime": str(datetime.now().replace(microsecond=0))
    })

class PGSql:
    def __init__(self):
        self.__host = globalVar.HOST
        self.__database = globalVar.DATABASE
        self.__user = globalVar.USER
        self.__password = globalVar.PASSWORD
        self.__port = globalVar.PORT

        try:
            self.__connection = psycopg2.connect(
                host = self.__host,
                database = self.__database,
                user = self.__user,
                password = self.__password
            )
            self.__cursor = self.__connection.cursor(cursor_factory=psycopg2.extras.DictCursor)
        except Exception as err:
            setResponse("500", f"ERROR : {err}")
    
    def select_one(self, query):
        self.__cursor.execute(query=query)
        return self.__cursor.fetchone()

    def select_all(self, query):
        self.__cursor.execute(query=query)
        return self.__cursor.fetchall()

    def get_column_names(self):
        return [colnames for colnames in self.__cursor.description]

@app.route("/list-data-drakor-json", methods=["POST", "GET"])
def list_data_drakor_json():
    pgsql = PGSql()
    datas = pgsql.select_all("""
        select 
            kdrama_name, kdrama_total_episode, kdrama_status, to_char(kdrama_publish_date, 'Mon dd, YYYY') as kdrama_publish_date,
            kdrama_rating, kdrama_where_to_watch, kdrama_image_url, kdrama_guarantee, kdrama_duration, kdrama_content_rating
        from list_kdrama
        order by kdrama_id asc
    """)

    list_data_drakor = []
    for data in datas:
        temp_data = {}
        for key, drakor in zip(pgsql.get_column_names(), data):
            temp_data.update({key[0]:drakor})
        list_data_drakor.append(temp_data)

    return list_data_drakor
    # return str(data)


if __name__ == "__main__":
    app.run(debug=True)